---
date: '2025-08-04T15:43:37-03:00'
draft: false
title: 'How Setup an easy blog with Hugo'
---


# Disclaimer

Go to main documentation if you need some more explanation, here I'll show the simple version to create this blog.

## Install Hugo

For Windows I recomend using Winget, on Powershell:

```powershell
winget install Hugo.Hugo.Extended
```

### Configure

Go to the folder in which you start your project and run this commands:

```powershell
# create a new project with yaml as configuration
hugo new site my-blog --format=yaml

cd my-blog
# set your github
hugo mod init github.com/username/my-blog

# add Hextra theme
hugo mod get github.com/imfing/hextra
```

I choose the Hextra theme and his startup are little different of the used in Hugo configuration as shown in the docs.

### Create the content pages

Create a new content page is very simple, just run the following commands:

```powershell
hugo new content/_index.md
hugo new content/docs/_index.md
```

### Preview
```powershell
hugo server --buildDrafts --disableFastRender
```

## Host on GitHub Pages

I recommend use the configuration for Hextra has in their documentation. [Here](https://imfing.github.io/hextra/docs/guide/deploy-site/).



{{% details title="Github Actions Configuration" closed="true" %}}

This is a configuration in august 2025, reach the site to get updated version.
```yaml {filename=".github/workflows/pages.yaml"} 
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.138.0
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # fetch all history for .GitInfo and .Lastmod
          submodules: recursive
      - name: Setup Go
        uses: actions/setup-go@v5
        with:
          go-version: '1.22'
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4
      - name: Setup Hugo
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

{{% /details %}}

## Cloudflare configurations + Github Pages

As I wanted my on domain, for learning and more beautiful presentation, I bought my domain and wanted to configure it.

As start a easy:

1. Buy **yourdomain.com**
2. Add DNS:
* Configure your root domain:
```text
Type: A | Name @ | Contend: 185.199.108.153 | Proxy: OFF (by now)
```
(Repeat for IPs: 185.199.109.153, 185.199.110.153, 185.199.111.153)
    
* Configure your subdomain
```text
Type: CNAME | Name: www | Contend: seuuser.github.io | Proxy: OFF (by now)
```
3. Configure Github Pages
* On your repo:
    * Settings -> Pages -> Custom Domain
    * Add **yourdomain.com** and save
* Wait ~30 minutes until:
    * ✅ "DNS check successful"
4. Activate HTTPS
* @ Github Pages activate:
    * Enforce HTTPS
* @ Cloudflare activate:
    * SSL/TLS -> Full (strict)
5. Redirect
* @ Cloudflare -> Rules -> Page Rules
```text
URL: seudominio.com/*
Action: Forwarding URL (301) → https://www.seudominio.com/$1
```

For validation you can run the command:
```bash
curl -I https://www.seudominio.com
```

Excpected Output:

```http
HTTP/2 200
x-github-request-id: ...
```

And this blog was born.