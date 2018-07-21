+++
title = "Automating GitHub Pages Deployment"
description = "Leveraging the Travis-CI GitHub Pages Provider to validate/build a website and deploy it."
tags = [
    "ci/cd",
    "travis-ci",
    "github pages"
]
date = "2018-07-21"
categories = [
    "Post"
]
+++

*Following the instructions for this post will enable you to use Travis-CI to deploy a Hugo-based website to GitHub Pages.*

When I set out to build the website for my blog, I knew I wanted to have the ability to dynamically generate my website as well as to be able to change the theme *(appearance)* of the website very easily. While researching the methods to accomplish this, two tools seemed to pop up the most:

* **[Hugo](https://gohugo.io/)**
* **[Jekyll](https://jekyllrb.com/)**

I ended up choosing Hugo, but either option is great. I recommend you do the research and choose for yourself (or just find a good blog post to follow).

As mentioned in a [previous post]({{< siteurl >}}2018/07/15/automate-website-with-travis-ci/), I use AWS S3, Route53, and CloudFront to host my website. You might be asking yourself then, "why is this guy writing about deploying to GitHub Pages then?" To answer that, because it's fun!

Truthfully, though, I accomplished two things researching and writing this post as I was able to learn more about GitHub Pages & Travis-CI as well as have a publicly accessible location for a test version of my website.

## GitHub Pages

[GitHub Pages](https://pages.github.com/) is a free service that allows static HTML website hosting, including the ability to support custom domains. The static HTML for the website is stored in a GitHub repository of your choosing.

It's important to note that there are 2 different types of GitHub Pages Sites - *User/Organization* and *Project*. I won't go into the specifics on the differences between these sites but you can find that information [here](https://help.github.com/articles/user-organization-and-project-pages/). For this guide, we'll be using an **User** GitHub Pages site.

## [Prerequisites for following the instructions below]

* A Hugo-based website
  * *Jekyll-based website can be used as well with minor changes to my instructions*
* GitHub repo used to store Hugo source files
* Travis-CI enabled for aforementioned GitHub repo [(see here)](https://docs.travis-ci.com/user/getting-started/#To-get-started-with-Travis-CI)
* *(optional)* - Custom Domain
* *(optional)* - DNS control of your custom domain