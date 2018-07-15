+++
title = "Automating Testing & Deployment of your Website"
description = "Leverage Travis-CI to validate/build a website and deploy it."
tags = [
    "ci/cd",
    "travis-ci",
    "route53",
    "cloudfront",
    "github",
    "s3"
]
date = "2018-07-15"
categories = [
    "Post"
]
+++

As websites go, mine is definitely in its infancy. This past week, I brought my website online by following a couple blog posts of some fellow colleagues ([Thom Greene](https://www.thomgreene.com/post/2018/2018-05-29-staticsitehugo/) & [Mike Tabor](https://miketabor.com/host-static-website-using-aws-s3/)). The help they provided was invaluable as I was able to register a domain and get a website going in a short period of time.

After following their instructions, I immediately knew what I wanted to do to add to their instructions. I wanted to add more Automation! *(Yes, I'm addicted...)* Here are the following automated features I set out to add to my website:

* Validation of Markdown
  * I'm pretty confident in my markdown-fu but everyone makes mistakes.
* Generation of website (using Hugo)
* Deployment of website to hosted S3 storage
* CloudFront Invalidation (tells AWS to pull in the latest content)

## *[Prerequisites for following the instructions below]*

* A Hugo-based website
* AWS S3 bucket used for back-end storage
* AWS CloudFront used to serve out website
* GitHub repo used to store Hugo source files

(My next post will show how to use Travis-CI for a GitHub pages setup.)