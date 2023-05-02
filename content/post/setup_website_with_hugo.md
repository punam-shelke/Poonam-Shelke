---
title: 'Setup website with hugo'
date: 2023-05-01T20:42:05+05:30
draft: false
---

# Setup your blog with Hugo

Hugo is a fast and flexible static site generator that helps you create websites without needing to know how to code. It uses Markdown to write content and offers a wide range of templates and themes to choose from.
In this blog, I will show you step by step guide on how I have setup this very site you are reading on.

## Install hugo

To create a site, first you have to [install hugo in your machine](https://gohugo.io/installation/).

Go to [hugo installation page](https://gohugo.io/installation/) and choose operating system you are working on. Follow the steps mentioned to install hugo.

Use below command to install hugo using brew in macos

```bash
brew install hugo
```

### Check hugo installation

To check if installation was successful, run hugo version command in your terminal

```bash
hugo version
```

If installtion is successful, you should see version number on your terminal.
Output will look like following format

```txt
hugo v0.108.0+extended darwin/amd64 BuildDate=unknown
```

## Create site

Once hugo is installed successfully, you can create a site with hugo. Run following command for the same.

```bash
hugo new site "Your blog name here"
```

### Initialise the site as git repository

For this you need git installed in your system. Go to git installation page and follow commands there. This will turn your site into git repository. It helps track any changes you make so that it is easier to go back if needed.
Run following commands for initialising your site as git repository

```bash
# go into the folder of site
cd your_blog_folder_name
# initialsing git repository
git init
# add files to git
git add .
# commit initial files
git commit -m "Initial commit"
```

## Chose a theme

This is the [full list of hugo themes](https://themes.gohugo.io/tags/blog/). Choose one that you like and follow documentation page to get started with the theme. I chose theme called as [github styles](https://themes.gohugo.io/themes/github-style/). Follow [documentation for github styles theme](https://github.com/MeiK2333/github-style)

### Add theme to site

```bash
git submodule add git@github.com:MeiK2333/github-style.git themes/github-style
```

### Use newly added theme

Add configuration in `config.toml`

```toml
theme = "github-style"
```

### Check if theme is working

Run hugo server and generate website to view in local

```bash
# Run hugo server
hugo server
# After this open url provided in log(most likely http://localhost:1313/) to open the webiste hosted by hugo server
```

<!-- TODO: add screenshots of the demo blog -->

## Customise blog details

There are few defaults you need to change for your blog. Make changes in config.toml to override default.

### Add title

```toml
title = "Poonam Shelke's blog"
```

### Add profile photo

Add image for your profile in `/static/images` folder. Add below line of code to refer to it in config.toml

```toml
[params]
avatar = "/images/your-profile-file-name.jpg"
```

### Add social media links

Add link to your social handles like github, facebook, linkedin, twitter, instagram etc.

```toml
github = "punam-shelke"
linkedin = "punam-shelke-636626140"
```

This is the [example_config.toml](https://github.com/MeiK2333/github-style/blob/master/config.template.toml) file for the reference.
This is config.toml for this blog which I use to customise this site.<!-- TODO: add link to github config.toml -->

```toml
baseURL = ""
languageCode = "en-IN"
title = "Poonam Shelke's blog"
theme = "github-style"

[params]
author = "Poonam"
description = "developer"
github = "punam-shelke"
linkedin = "punam-shelke-636626140"
location = "India"
userStatusEmoji = "❤️"
avatar = "/images/profile.jpg"
url = "https://google.com"        #TODO: change the url

[frontmatter]
lastmod = ["lastmod", ":fileModTime", ":default"]
```

## Write content

This section will enable you to write content for your blog and make it visible on hugo site.

### Create a post

```bash
hugo new post/name_of_your_post.md
```

you will notice above command will create a markdown file in `content/post` folder with front matter. Front matter is the basic information about your blog. Add the content for your blog in this file.

```md
<!-- front matter -->

---

title: 'Setup website with hugo'
date: 2023-05-01T20:42:05+05:30
draft: true

---

Your content goes here...
```

---

**NOTE**

`post` folder is very specific to `github-styles` theme. Please check documentation of your theme. It could be called `posts`

---

### Make post visible on website

Once you are done editing the blog, set `draft` to `false` in front matter. This will make post visible in the site.

## Publish the site

Published site on github pages using github actions by following [hugo documentation](https://gohugo.io/hosting-and-deployment/hosting-on-github/).

Push the hugo site to github repository. Set base url in config.toml file to

```toml
baseURL = "your-github-username.github.io/Repository-name-where-site-is-pushed"
```

Enable github actions in settings
Go to `Settings` > `Pages` > `Build and deployment` and select source to `Github Actions`

Add `.github/workflows/hugo.yaml` configration file in your git repository as follows and change default branch and hugo version as needed.

```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

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
  group: 'pages'
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
      HUGO_VERSION: 0.111.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Install Dart Sass Embedded
        run: sudo snap install dart-sass-embedded
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Install Node.js dependencies
        run: '[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true'
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
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
        uses: actions/deploy-pages@v2
```

Once you push the changes, you will be able to see github actions starting deployment of your hugo site.
And it will be available on your base URL.
