---
date: '2025-08-04T12:23:00-03:00'
draft: true
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

