# Portfolio Theme for Hugo

A modern, responsive portfolio theme built with Hugo and Tailwind CSS, featuring dark mode support.

## Features

- **Dark Mode** - System-aware dark mode with manual toggle
- **Tailwind CSS** - Utility-first CSS framework for rapid styling
- **Responsive Design** - Mobile-first approach that looks great on all devices
- **SEO Optimized** - Built-in Open Graph and Twitter Card support
- **Fast** - Minimal JavaScript, optimized for performance
- **Accessible** - WCAG compliant with proper ARIA labels

## Installation

1. Add this theme to your Hugo site:

```bash
git submodule add https://github.com/beingadityak/portfolio-theme themes/portfolio
```

2. Update your `hugo.toml`:

```toml
theme = 'portfolio'
```

3. Install dependencies and build CSS:

```bash
cd themes/portfolio
npm install
npm run build
```

## Development

To watch for CSS changes during development:

```bash
cd themes/portfolio
npm run dev
```

In another terminal, run Hugo server:

```bash
hugo server -D
```

## Configuration

### Site Parameters

Add these to your `hugo.toml`:

```toml
[params]
  author = "Your Name"
  description = "Your tagline"
  github = "https://github.com/username"
  linkedin = "https://linkedin.com/in/username"
  twitter = "https://twitter.com/username"
  email = "hello@example.com"
  
  # Google Analytics (GA4)
  googleAnalytics = "G-XXXXXXXXXX"
  
  # Google AdSense
  googleAdsense = "ca-pub-XXXXXXXXXXXXXXXX"
```

### Google Analytics

Add your GA4 measurement ID to enable Google Analytics tracking:

```toml
[params]
  googleAnalytics = "G-XXXXXXXXXX"
```

Analytics is automatically disabled in development mode (`hugo server`).

### Google AdSense

Add your AdSense publisher ID to enable ads:

```toml
[params]
  googleAdsense = "ca-pub-XXXXXXXXXXXXXXXX"
```

To display ads in your content, use the `adsense-ad` partial:

```html
{{ partial "adsense-ad.html" (dict "context" . "slot" "1234567890") }}
```

Parameters:
- `slot` (required): Your ad slot ID
- `format`: Ad format - "auto", "fluid" (default: "auto")
- `responsive`: Whether the ad is responsive (default: true)
- `style`: Custom inline styles

Example with all options:

```html
{{ partial "adsense-ad.html" (dict "context" . "slot" "1234567890" "format" "auto" "responsive" true "style" "display:block; margin:20px 0") }}
```

AdSense is automatically disabled in development mode.

### Menu

```toml
[menu]
  [[menu.main]]
    name = "Home"
    url = "/"
    weight = 1
  [[menu.main]]
    name = "About"
    url = "/about/"
    weight = 2
  [[menu.main]]
    name = "Projects"
    url = "/projects/"
    weight = 3
  [[menu.main]]
    name = "Blog"
    url = "/blog/"
    weight = 4
  [[menu.main]]
    name = "Contact"
    url = "/contact/"
    weight = 5
```

## Content Types

### Projects

Create project files in `content/projects/`:

```markdown
---
title: "Project Name"
description: "Brief description"
date: 2024-01-01
tags: ["Go", "AWS"]
github: "https://github.com/..."
demo: "https://demo.example.com"
image: "/images/project.jpg"
---

Project content here...
```

### Blog Posts

Create blog posts in `content/blog/`:

```markdown
---
title: "Post Title"
description: "Brief description"
date: 2024-01-01
tags: ["Tutorial", "Go"]
image: "/images/post.jpg"
---

Post content here...
```

### About Page

Create `content/about.md` with layout "about":

```markdown
---
title: "About Me"
layout: "about"
skills:
  - "Go"
  - "Python"
  - "AWS"
experience:
  - title: "Job Title"
    company: "Company Name"
    period: "2022 - Present"
    description: "What you did"
---

About content here...
```

## Customization

### Colors

Edit `tailwind.config.js` to customize the color palette:

```javascript
theme: {
  extend: {
    colors: {
      primary: {
        // Your custom colors
      }
    }
  }
}
```

### Fonts

The theme uses Inter for body text and JetBrains Mono for code. Modify in `layouts/partials/head.html`.

## License

MIT License - see LICENSE file for details.
