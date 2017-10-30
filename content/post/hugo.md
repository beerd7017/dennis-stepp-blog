+++
title = "Create Your Static Site with Hugo"
description = "Discover how to create a static site with Hugo, a static site generator."
date = "2017-10-28"
categories = ['JAMStack', 'JavaScript', 'API', 'Markdown']
tags = ['Hugo', 'GitHub', 'Netlify']
thumbnail = "img/posts/hugo/hugo.png"
+++

There's many different options when it comes to generating a static site, [Jekyll](https://jekyllrb.com/), [Hexo](https://hexo.io/), [Gatsbyjs](https://github.com/gatsbyjs/gatsby), [MkDocs](http://www.mkdocs.org/), [Wyam](https://wyam.io/), and many more. So why did I choose to create this blog with [Hugo](http://gohugo.io/)?

No reason. I just did. All of these options are viable for creating something like a blog, but I do suggest if your use case is more complicate and you have some experience with [React](https://reactjs.org/), then do check out [Gatsbyjs](https://github.com/gatsbyjs/gatsby).

# Let's Get Started

First off, you're going to need to have [Git](https://git-scm.com/) and hugo installed. If you haven't used [Chocolatey](https://chocolatey.org/), this is so easy to install hugo. Simply open a PowerShell console and run this command.

    choco install hugo -confirm
    
Now that's hugo is installed, you can create your new site

    hugo new site myHugoSite

# Make it pretty
    
If you're like me *.css* is not a strong suit. But behold this magic:  Head on over to [themes.gohugo.io](https://themes.gohugo.io/) and pull the git repo on whatever theme you like best into your `themes` folder in your project:

    cd myHugoSite
    
    git init;
    
    git submodule add https://github.com/Vimux/mainroad

Now open your `config.toml` configuration file and set the theme to  `mainroad`.

    theme = "mainroad"

# Write it up

Create a new markdown file and write away.

    hugo new posts/firstPost.md
    
# Take a gander

You can start a development server to see what your content looks like:

    hugo server
    
# Commit your site to GitHub

When you're ready commit your source code to your GitHub repository.

# Deploy to Netlify

![netlify](/img/posts/hugo/netlify-content.jpg "Netlify")

Sign up on [Netlify](https://app.netlify.com/signup) with your GitHub account. You can hookup your Git repo to Netlify and literally deploy your site just by committing your source code.

**Did I mention that all of this is free?**          

