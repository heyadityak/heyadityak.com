---
title: "Getting Started with Kubernetes"
date: 2024-12-15
description: "A beginner's guide to understanding Kubernetes concepts and deploying your first application"
image: "/images/blog/flower.svg"
tags:
  - "Kubernetes"
  - "DevOps"
  - "Containers"
  - "Cloud Native"
---

Kubernetes has become the de facto standard for container orchestration. In this guide, we'll explore the fundamental concepts and get you started with deploying your first application.

## What is Kubernetes?

Kubernetes (often abbreviated as K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. Originally developed by Google, it's now maintained by the Cloud Native Computing Foundation (CNCF).

![Kubernetes Cluster Architecture](/images/blog/kubernetes-cluster-architecture.svg)

The diagram above shows the high-level architecture of a Kubernetes cluster, including the control plane components and worker nodes.

## Core Concepts

### Pods

A Pod is the smallest deployable unit in Kubernetes. It represents a single instance of a running process in your cluster and can contain one or more containers.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: my-container
    image: nginx:latest
    ports:
    - containerPort: 80
```

### Deployments

Deployments provide declarative updates for Pods. You describe the desired state, and the Deployment controller changes the actual state to match.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

### Services

Services expose your application to network traffic. They provide a stable endpoint for accessing Pods, even as Pods are created and destroyed.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

## Getting Started

### 1. Install kubectl

First, install the Kubernetes command-line tool:

```bash
# macOS
brew install kubectl

# Linux
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

### 2. Set Up a Local Cluster

For local development, you can use minikube or kind:

```bash
# Using minikube
brew install minikube
minikube start

# Using kind
brew install kind
kind create cluster
```

### 3. Deploy Your First App

```bash
# Create a deployment
kubectl create deployment hello-world --image=nginx

# Expose it as a service
kubectl expose deployment hello-world --port=80 --type=NodePort

# Check the status
kubectl get pods
kubectl get services
```

## Best Practices

1. **Use namespaces** to organize resources and manage access control
2. **Set resource limits** to prevent runaway containers from consuming all resources
3. **Use liveness and readiness probes** to ensure your application is healthy
4. **Implement proper logging and monitoring** with tools like Prometheus and Grafana
5. **Use ConfigMaps and Secrets** for configuration management

## Conclusion

Kubernetes provides a powerful platform for running containerized applications at scale. While the learning curve can be steep, understanding these core concepts will give you a solid foundation to build upon.

In future posts, we'll dive deeper into advanced topics like Helm charts, custom resources, and production-ready configurations.

Happy orchestrating! 🚀
