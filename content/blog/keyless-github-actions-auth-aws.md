---
title: "Keyless Github Actions Authentication to AWS"
date: 2023-02-16
description: "Perform keyless authentication and authorisation to AWS services, without having to manage separate access keys."
image: "/images/blog/github-actions-auth-blog/cover.jpg"
tags: ["Github Actions", "AWS", "Tutorial", "SAML", "OIDC"]
---

## Motivation

Whenever we're configuring GitHub Actions for our CD actions to AWS cloud (for example, syncing our front-end code, running Terraform etc.), as with any other CI/CD provider (TravisCI, CircleCI, Jenkins etc.) we generally create a separate IAM user who might be having strong permissions to work with AWS services to achieve our task.

This introduces a few caveats:

* You have to manage the rotation lifecycle of this access key pair and change the values in the places which are used by GitHub Actions.
    
* You're creating a risk to have these credentials stored in somebody's machine. Since these credentials are sometimes fairly powerful, anyone who is having access to these keys can abuse the permissions granted to this user.
    

This article explains the process of configuring authentication to AWS from GitHub Actions without having to create the user and have a credential less experience for interactions with AWS.

## High-level steps

Following are the high-level steps you need to perform to have a credential-less experience in interacting with AWS cloud from GitHub Actions:

1. Configure GitHub as an OIDC provider in AWS
    
2. Create an IAM role that allows only GitHub Actions to interact with it.
    
3. Assign the permissions to the IAM role
    
4. Add the IAM role as a GitHub Actions secret
    
5. Configure the IAM role in your pipeline to interact with AWS
    

We will now go over each of the above-mentioned steps one at a time which you can follow to successfully configure the IAM role-based access for your GitHub Action.

For this blog's example, we'll be configuring a workflow to deploy a static application to an S3 bucket with CloudFront as a CDN (for a custom domain to our website). All the code is available in this [GitHub repository](https://github.com/heyadityak/gh-actions-keyless-aws).

## Tasks to be performed by GitHub Actions

As a part of our "deployment" process for this demonstration, we'll be performing the following tasks as a part of our build & release pipeline:

1. Perform a basic test (on all pushes).
    
2. If it's being deployed from the `main` branch:
    
    1. Sync the static files to S3.
        
    2. Perform a CloudFront invalidation to have the latest version available to users.
        

As you can see, we are having a few steps which require interaction with S3 and CloudFront.

Let's proceed with how to configure the items mentioned in the high-level steps.

## Configure the experience

### Configure GitHub as an OIDC provider in AWS

This section is the main part of the credential-less workflow which needs to be configured with *caution* as this essentially allows GitHub to authenticate with AWS, and the dynamically generated GitHub Actions *user* can then *assume* the IAM role (which we configure in the later steps) to interact with AWS.

By configuring an OIDC (OpenID Connect) provider within AWS, you're establishing trust between GitHub.com and AWS. You may have heard of this term when you're having a **Login with Google** or a **Login with Facebook** button when developing your application, where you're allowing Facebook or Google users to sign-up/sign in to your application. Here we're essentially doing the same thing, where we allow the GitHub Actions *user* to use AWS services, without having to statically create the user in AWS first.

*Okay, enough talk*. Let's jump right into the configuration.

Once you log in to your AWS console, go to IAM &gt; Identity Providers. You'll find an empty list (ignore the item listed in my screenshot, that's because I'm using [AWS Identity Center](https://aws.amazon.com/iam/identity-center/) for managing my accounts and users).

![](/images/blog/github-actions-auth-blog/step1.png)

Click on `Add Provider` and after selecting `OpenID Connect` as the provider type, you'll be presented with the provider configuration screen.

![](/images/blog/github-actions-auth-blog/step2.png)

Now, configure the GitHub provider as mentioned in the below order:

1. In the `Provider URL` provide `https://token.actions.githubusercontent.com` as the value.
    
2. Click on `Get Thumbprint` for getting the certificate and validity of GitHub Actions OIDC provider URL.
    
3. In the `Audience` provide `sts.amazonaws.com` as the value.
    

Post configuration of the provider as per the above steps, your screen should look something like this:

![](/images/blog/github-actions-auth-blog/step3.png)

Click on `Add Provider` and you've just set GitHub Actions as your OIDC provider.

### Configure the IAM role to use with the OIDC provider

Now, once you've configured the GitHub Actions OIDC provider, it is mandatory to have an associated IAM role which will be used by the GitHub Actions' dynamic user (the technical term is ***token***) to interact with AWS.

Follow these steps to configure the IAM role to be used with GitHub Actions:

1. Under IAM, go to "Roles" and create a role with the trusted entity as "Web Identity" and select the previously created OIDC provider and the audience mentioned
    
2. ![](/images/blog/github-actions-auth-blog/step4.png)
    
    Select the IAM policies as per your requirement (for this demo, I'm going with `AmazonS3FullAccess` and `CloudFrontFullAccess` as I'm lazy to create a custom IAM policy)
    
3. Provide a role name and create the role (we'll be changing the trust policy to scope the grant to specific GitHub repositories and branch names)
    
    ![](/images/blog/github-actions-auth-blog/step5.png)
    
4. Change the IAM trust policy as follows:
    
    ```json
    {
    	"Version": "2012-10-17",
    	"Statement": [
    		{
    			"Effect": "Allow",
    			"Principal": {
    				"Federated": "arn:aws:iam::123456789012:oidc-provider/token.actions.githubusercontent.com"
    			},
    			"Action": "sts:AssumeRoleWithWebIdentity",
    			"Condition": {
    				"StringLike": {
                        "token.actions.githubusercontent.com:sub": "repo:<GITHUB_ORG_NAME>/<GITHUB_REPO_NAME>:ref:refs/heads/<GITHUB_BRANCH_NAME>"
                    },
                    "StringEquals": {
                        "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
                    }
    			}
    		}
    	]
    }
    ```
    
    Here for the account number under "Federated", change it to your AWS account, and change the placeholders for `GITHUB_ORG_NAME`, `GITHUB_REPO_NAME` and `GITHUB_BRANCH_NAME` to your values. Optionally if you want to allow this role to be used by all branches in your repository you can add a wildcard `*` After the repo name.
    
5. Now, you're all set for allowing the GitHub Actions token to authenticate and interact with AWS services.
    

### Add the IAM role as a GitHub Actions Secret

Once we've configured the IAM role, we can simply add the role ARN (Amazon Resource Name) as a secret in our repository (or at the GitHub Organisation level if you plan to use the role for multiple repositories).

Go to your repository's settings and under Secrets and variables &gt; Actions and create a repository secret

![](/images/blog/github-actions-auth-blog/step6.png)

Create a new secret with `GH_ACTIONS_AWS_ROLE` as the key and your role ARN as the value.

![](/images/blog/github-actions-auth-blog/step7.png)

Now, we'll proceed and configure the GitHub Actions pipeline to perform our deployment to S3 static website.

### Deployment to AWS - In Action

Now let's configure our pipeline to use the keyless authentication experience. The pipeline is available in my [GitHub repository](https://github.com/heyadityak/gh-actions-keyless-aws/blob/main/.github/workflows/deploy.yml).

We need to take care of the following items in the pipeline file:

```json
permissions:
  id-token: write
  contents: read
```

Here, we're requesting the JWT from GitHub's OIDC provider to authenticate to AWS and we're fetching the OIDC token at the workflow level.

We've also added the following repository secrets for this demo:

![](/images/blog/github-actions-auth-blog/step8.png)

Once all the variables are set, we commit our GitHub Actions workflow (along with the source code). We can check the run below:

![](/images/blog/github-actions-auth-blog/step9.png)

And here's the final deployment of our static website:

![](/images/blog/github-actions-auth-blog/step10.png)

## Conclusion

With the above-mentioned steps, you can now easily configure a keyless experience for authenticating with AWS Cloud from GitHub Actions without having to configure and manage a separate pair of AWS access key pairs for GitHub.

Hope you've liked the above article and feel free to share this with your network if you've found this helpful.

Feel free to connect with me on [LinkedIn](https://www.linkedin.com/in/heyadityak) as well.

![](/images/blog/github-actions-auth-blog/end.png)