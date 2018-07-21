+++
title = "Automating GitHub Pages Deployment"
description = "Leveraging Travis-CI & GitHub Pages build and host a website."
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

It's important to note that there are 2 different types of GitHub Pages Sites - *User/Organization* and *Project*. I won't go into the specifics on the differences between these sites but you can find that information [here](https://help.github.com/articles/user-organization-and-project-pages/). For this guide, we'll be using a GitHub Pages **User** site.

## [Prerequisites for following the instructions below]

* A Hugo-based website
  * *Jekyll-based website can be used as well with minor changes to my instructions*
* GitHub repo used to store Hugo source files
* Travis-CI enabled for aforementioned GitHub repo [(see here)](https://docs.travis-ci.com/user/getting-started/#To-get-started-with-Travis-CI)
* *(optional)* - Custom Domain
* *(optional)* - DNS control of your custom domain
* *(optional)* - Base knowledge of DNS

## Automation Time

Automating this deployment is super easy and can be accomplished in just a few steps. I'll cover these in the sections below.

### 1. GitHub Configuration

GitHub has really good [documentation](https://pages.github.com/) on how to setup a GitHub Pages site. I'll cover the basic steps to get setup and going.

#### Create a GitHub Repository for your Static HTML Website

In order to create a GitHub Pages *User* site, you'll need to create a GitHub repo using the following naming scheme:

```text
<github-username>.github.io
```

For example, my GitHub profile is: github.com/**carceneaux**

So the GitHub repo name for my site is:

```text
carceneaux.github.io
```

**Note:** No further configuration of the newly created GitHub repo is necessary.

### 2. Create DNS Record for Custom Domain (optional)

If you have a custom domain that you'd like to leverage with GitHub Pages, you'll need to create a [CNAME record](https://en.wikipedia.org/wiki/CNAME_record) for your domain in your DNS configuration.

Depending on what online service your using to manage DNS for your domain, the steps to do this will vary. The typical process follows as such:

* Select the domain where you want to administer DNS
  * For me, that's **arsano.ninja**
* Create New Record
* For the type of record, you'll choose **CNAME**
* The *Name* of the new record should be the domain name
  * ie. **arsano.ninja**
  * If you want to use a subdomain, the name would be: **subdomain.arsano.ninja**
* The value of the new record will be the name of the new GitHub repo you built previously.
  * ie. `<github-username>.github.io`

**Note:** It can sometimes take up to 48 hours for DNS changes to propogate throughout the internet.

### 3. Travis-CI Configuration

While I'm going to focus on the configuration and code needed to accomplish this task, I highly recommend you reference the [Travis-CI Deployment Documentation](https://docs.travis-ci.com/user/deployment) as that team has done an excellent job and it will allow you to see the built-in capability of Travis-CI.

#### Define Environment Variables within the Travis-CI Web UI

**Note:** You could define these variables straight in the `.travis.yml` but then you'd have to encrypt them following [this process](https://docs.travis-ci.com/user/environment-variables/#Defining-encrypted-variables-in-.travis.yml). Per Travis-CI docs, the only time it makes sense to put encrypted variables in the `.travis.yml` is when the variable will change depending on the branch being committed.

* In the [Travis-CI Web UI](https://travis-ci.org/), edit the **Settings** for the GitHub repo with your Hugo source files.
* Add the following environment variable using this *exact* name:
  * **GITHUB_TOKEN**
    * You create a GitHub Personal Access token [here](https://github.com/settings/tokens).

Note: Make sure to leave the Travis-CI environment variable on its default value for *Display value in build log*. ***NEVER*** display your GitHub personal access token on the internet.

#### Code for Travis-CI

Travis-CI configuration is stored in a file named `.travis.yml` in the root folder of your GitHub repo with your Hugo source files. Here's the necessary code for the `.travis.yml` file:

```yml
### Installing pre-requisites
install:
    - wget https://github.com/gohugoio/hugo/releases/download/v0.44/hugo_0.44_Linux-64bit.deb
    - sudo dpkg -i hugo*.deb

### Building website
script:
    - hugo

### Sending newly generated website to GitHub Pages site
deploy:
  # Control deployment by setting a value for `on`. Setting the `branch`
  # option to `master` means Travis will only attempt a deployment on
  # builds of your repo's master branch (e.g., after you merge a PR).
  provider: pages
  on:
    branch: master
  # GitHub Repo to deploy to
  repo: "carceneaux/carceneaux.github.io"
  # Target branch to commit to
  target-branch: "master"
  # Sets the custom domain for my GitHub page
  fqdn: test.arsano.ninja
  # Prevent Travis from deleting your built site so it can be uploaded.
  skip-cleanup: true
  # GitHub personal access token
  github-token: $GITHUB_TOKEN  # Set in the settings page of your repository, as a secure variable
  # Path to a directory containing your built site.
  local-dir: public
```

Please reference [Travis-CI Documentation](https://docs.travis-ci.com/user/deployment/pages/) for an explanation of the options I've chosen.

Defining options:

* **repo** - This will be defined in the following format:
  * `<github-username>/<github-username>.github.io`
* **fqdn** - This is your custom domain name. Whatever value you chose when creating your CNAME record needs to be entered here.
  * In case you don't have a custom domain and you skipped step #1, you can omit `fqdn` from your configuration. All this means is that the URL to your website will be: `https://<github-username>.github.io`

**Super Cool Highlight:** If you specify a `fqdn`, GitHub automates SSL certificate creation for your site using [Let's Encrypt](https://letsencrypt.org/). It's not immediate, but for my site, the newly created valid SSL cert was in place within 4 hours. I'm sure it takes less than that but my power was knocked out immediately after making my first commit...

## Putting Everything Together

As mentioned at the beginning of this article, I was looking to use the code discussed today to create a test site for my website. The code in the `.travis.yml` below is an add-on from my [previous post]({{< siteurl >}}2018/07/15/automate-website-with-travis-ci/) where I discussed automated markdown testing and deployment to AWS S3 & CloudFront.

```yml
### Python is necessary for AWS CLI
language: python
python:
  - "3.6.5"

### Installing pre-requisites
install:
    - wget https://github.com/gohugoio/hugo/releases/download/v0.44/hugo_0.44_Linux-64bit.deb
    - sudo dpkg -i hugo*.deb
    - pip install awscli
    - gem install mdl

### Testing markdown and building website
script:
    - mdl -r ~MD002,~MD013,~MD033 content/ # rules that have been exluded
    - if [[ $TRAVIS_BRANCH == "master" ]]; then hugo; else hugo --config=test.toml; fi


### Sending newly generated website to S3 bucket
deploy:
  # Control deployment by setting a value for `on`. Setting the `branch`
  # option to `master` means Travis will only attempt a deployment on
  # builds of your repo's master branch (e.g., after you merge a PR).
  - provider: s3
    on:
      branch: master
    # You can refer to environment variables from Travis repo settings!
    access_key_id: $AWS_ACCESS_KEY_ID
    secret_access_key: $AWS_SECRET_ACCESS_KEY
    # Name of the S3 bucket to which your site should be uploaded.
    bucket: $S3_BUCKET
    # Prevent Travis from deleting your built site so it can be uploaded.
    skip_cleanup: true
    # Path to a directory containing your built site.
    local_dir: public
    # Set the Cache-Control header.
    cache_control: "max-age=21600"
  - provider: pages
    on:
      all_branches: true
      condition: $TRAVIS_BRANCH != "master"
    # GitHub Repo to deploy to
    repo: "carceneaux/carceneaux.github.io"
    # Target branch to commit to
    target-branch: "master"
    # Sets the custom domain for my GitHub page
    fqdn: test.arsano.ninja
    # Prevent Travis from deleting your built site so it can be uploaded.
    skip-cleanup: true
    # GitHub personal access token
    github-token: $GITHUB_TOKEN  # Set in the settings page of your repository, as a secure variable
    # Path to a directory containing your built site.
    local-dir: public

### Forcing AWS CloudFront to refresh its cache
after_deploy:
  # Invalidate every object in the targeted distribution.
  - if [[ $TRAVIS_BRANCH == "master" ]]; then aws cloudfront create-invalidation --distribution-id $CLOUDFRONT_DISTRIBUTION_ID --paths "/*"; fi
```

This new code allows Travis-CI to distinguish between my *testing* and *production* website code. Basically, my `master` branch is where the source code for *[www.arsano.ninja](https://www.arsano.ninja)* is kept and any other branch is deployed to *[test.arsano.ninja](https://test.arsano.ninja)*. You'll notice some simple conditional logic in my `.travis.yml` to allow this behavior.