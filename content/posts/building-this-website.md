---
title: "Building and Deploying this website"
date: 2021-09-11T12:32:01+08:00
description: "Steps for building and deploying your own hugo Website/Blog"
tags: ["hugo", "static", "website", "building", "deploying"]
type: post
---

## Getting started

First things first, in order to work with Hugo you need to install it.
While there are multiple ways to do so, the easiest is to just download the pre-compiled binaries for your platform.

Go to: [Install Hugo](https://gohugo.io/getting-started/installing/) and follow the instructions.

### A. Create Your Project

Hugo provides a `new` command to create a new website:

```bash
hugo new site my_website
cd my_website
```

### B. Install a Theme

In order to install a theme for hugo, you simply have to download the submodule inside the `themes/` folder of your project.

This theme’s repository is: [https://github.com/526avijitgupta/gokarna](https://github.com/526avijitgupta/gokarna).

```bash
git init
git submodule add https://github.com/526avijitgupta/gokarna.git themes/gokarna
```

### C. Basic Configuration

Every hugo website has a `config.toml` in its home directory. Inside this file you'll find a miriad of options to configure your website.

```toml
baseURL = "http://example.org/"
defaultContentLanguage = "en"
languageCode = "en"

title = "My New Hugo Site"

theme = "gokarna"

# Automatically generate robots.txt
enableRobotsTXT = true

[menu]
  [[menu.main]]
    # Unique identifer for a menu item
    identifier = "posts"

    url = "/posts/"
    
    # You can add extra information before the name (HTML format is supported), such as icons
    pre = ""

    # You can add extra information after the name (HTML format is supported), such as icons
    post = ""

    # Display name
    name = "Posts"

    # Weights are used to determine the ordering
    weight = 1

  [[menu.main]]
    identifier = "tags"
    name = "Tags"
    url = "/tags/"
    weight = 2
    
  [[menu.main]]
    identifier = "github"
    url = "https://github.com"
    weight = 3
    
    # We use feather-icons: https://feathericons.com/
    pre = "<span data-feather='github'></span>"
```

### D. Creating content

All hugo content lives inside the `content/` directory, in order to create the pages for each content type, you first need to create this folder. 

Then, inside of `content/` create a subfolder for each content type you wanna have in your website (_for this theme we have `posts/` and `projects/`_).

Lastly you need to create some content.

Lets say you wanna create a new Post, you can either create an empty `.md` file and fill it by hand, or you can use the hugo CLI:

```bash
hugo new posts/first_post.md
```

Feel free to edit the post file by adding some sample content and replacing the title value in the beginning of the file.

For posts you need to add `type: "post"` in the markdown metadata.


### E. Developing locally

Start the development server by running:

```bash
hugo serve
```

Go to `http://localhost:1313`.

### F. Building for deployment

When your site is ready to deploy, run the following command:

```bash
hugo
```

A `public` folder will be generated, containing all static content and assets for your website. It can now be deployed on any web server.

The website can be automatically published and hosted with [Netlify](https://www.netlify.com/), [AWS Amplify](https://gohugo.io/hosting-and-deployment/hosting-on-aws-amplify/), [Github pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/), [Render](https://gohugo.io/hosting-and-deployment/hosting-on-render/) and more...

For me, it's way easier to simply contain the source code in a single repository and deploy the website to [Github pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/).

## Configuration
In addition to [Hugo global configuration](https://gohugo.io/overview/configuration/) and [menu configuration](#basic-configuration), **gokarna** lets you define the following parameters in your site configuration (here is a `config.toml`, whose values are default).

```toml
[params]
  # URL for the avatar on homepage
  avatarURL = ""

  # Choose one of size-xs, size-s, size-m, size-l & size-xl. (Default: size-m)
  avatarSize = ""

  # Description to display on homepage
  description = "Sky above, sand below & peace within"

  # Accent color is displayed when you hover over <a> tags
  accentColor = "#FF4D4D"

  # You can use this to inject any HTML in the <head> tag.
  # Ideal usecase for this is to import custom js/css or add your analytics snippet
  customHeadHTML = ""

  # Keywords relevant for SEO
  metaKeywords = ["blog", "gokarna", "hugo"]

  # If you want to display posts on the homepage, the options are
  # "popular" (order posts by weight), "recent" (order posts by date)
  # or "" (do not display, default option)
  showPostsOnHomePage = ""

  # Footer text
  footer = "The Marauders"
```

### Static content

Hugo automatically serves static content that exists inside a `static/` folder in you project root directory. When you first create a hugo project, no such folder will exist, so you need to create it.

E.x. If you want to serve static images (such as your avatar), you need to create `static/images/` inside your project root directory and put inside of there your images, lets say `avatar.jpg`

Then you can reference this image from your configuration file such as:
```toml
[params]
  avatarURL = "/images/avatar.jpg"
```

## Deploying on Github

I chose to deploy this Website on Github, because i find it simplier to contain your source code & your build files in a single repository.

### Github Pages

With the release of [Github Pages](https://pages.github.com/) you can now host simple static sites for your projects inside of github.

In general if you name your repository `${your_name}.github.io` and push to github it will automatically start a workflow for you and deploy your site to `https://${your_name}.github.io`

For example:

If you have a simple repo with the file `index.html` inside of it and you follow the abovementioned naming convention, when you push to github you will be able to view your website.

### Deploying Hugo with Github Pages

The simple way to deploy your hugo website to github is to simply have 2 separate repositories.  
The first repository will contain your source code which you can name whatever you want.  
The second repository will contain the built/ready to serve files produced by ``` hugo -D ``` when building your project.  
The `hard` way to do it is to keep a single repository and by utilizing the power of [Github Actions](https://docs.github.com/en/actions/learn-github-actions) instruct Github to build your website (`Run: hugo -D`), produce the final files, check them out to a different branch and deploy them.

Essentially Github needs a folder to deploy, if you chose to go with the second method, which i think is better since you maintain only a single repository, you will have to produce this folder somehow and by automating the process you will instruct Github to produce this folder for you and keep it in a separate branch for you and deploy it all by itself every time you push to your repository.

#### a. Disabling default workflow
Since by default github pages will try to built your site with Jekyll, you have to disable this action in order to provide your own instructions on how to built the source code and deploy it as explained here [Deploy Hugo on Github](https://gohugo.io/hosting-and-deployment/hosting-on-github/)

In order to do so, simply create an empty `.nojekyll` file in the root directory of your project

#### b. Providing your own workflow
Now, you have to provide your own workflow.  
Create a subdirectory for your files:
```bash
mkdir .github/workflows
```

Next, create a file for your new workflow
```bash
touch .github/workflows/gh-pages.yml
```

You can name this file whatever you want, but it has to be a `YAML` file.

Now follow the instructions in [Deploy Hugo on Github](https://gohugo.io/hosting-and-deployment/hosting-on-github/) and put your instructions inside the `.yml` file

Push to your remote repository

```bash
git push -u origin master
```

This will trigger a Github Workflow to start and you will be able to see its progress by navigating to your repo's path and clicking on `Actions`

#### c. Final steps

Now, this part took me a while to debug. Github will produce a separate branch `gh-pages` and put the built files in there and actually push to it. But you will have to take one final step to actually see the content.

In your project's Github Page, go to `User` -> `settings` -> `Pages` and then change the `source` branch of your project to point to `gh-pages` instead of `master/main`. I wasted a couple of hours trying to figure why my build was not showing up on the web :)

After all this, you and everybody else can finally enjoy your shiny new Website.