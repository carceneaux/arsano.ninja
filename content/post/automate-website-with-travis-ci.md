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

After following their instructions, I immediately knew what I wanted to do to add to their instructions....I wanted to add more Automation! *(Yes, I'm addicted...)*

Here are the following automated features I set out to add to my website:

* Validation of Markdown
  * I'm pretty confident in my markdown-fu but everyone makes mistakes.
* Generation of website (using Hugo)
* Deployment of website to hosted S3 storage
* CloudFront Invalidation (tells AWS to pull in the latest content)

## Travis-CI

I knew that I needed a CI/CD solution to fulfill any of my features and, most of all, I wanted it to be *free*. With that, I found [Travis-CI](https://travis-ci.org/). It was easy to learn and very robust. To be honest, I've been immensely impressed with Travis-CI.

## [Prerequisites for following the instructions below]

* A Hugo-based website
* AWS S3 bucket used for back-end storage
* AWS CloudFront used to serve out website
* GitHub repo used to store Hugo source files
* Travis-CI enabled for aforementioned GitHub repo [(see here)](https://docs.travis-ci.com/user/getting-started/#To-get-started-with-Travis-CI)

(My next post will show how to use Travis-CI for a GitHub pages setup.)

## Automating the Thangs

Each of the features I wanted to automate you can find in its own section below.

### 1. Validation of Markdown

With a little googling, I found a Ruby-based [Markdown Lint tool](https://github.com/markdownlint/markdownlint) that was pretty solid. Better yet, from watching the Travis-CI build logs, I knew that ruby was already installed in the base testing environment.

With that in mind, I only had to include the following lines in my `.travis.yml`:

```yml
### Installing pre-requisites
install:
    - gem install mdl

### Testing markdown
script:
    - mdl -r ~MD002,~MD013,~MD033 content/ # these rules have been exluded
```

That's it! Easy peasy lemon squeezy!

You'll notice that I excluded some of the rules when checking. I did this as I found these rules were not beneficial for a website. On another note, I also recommend you install a Markdown Lint program on your IDE as well. *(I use [Microsoft Visual Studio Code](https://code.visualstudio.com/) for my IDE and [markdownlint](https://github.com/DavidAnson/vscode-markdownlint).)*

### 2. Generation of Website using Hugo

The more you use Travis-CI, the more you'll find there are multiple methods to accomplish your goal. I think this speaks volumes to how powerful the tool really is. I ended up finding another [article](https://jellis18.github.io/post/2017-12-03-continuous-integration-hugo/) online that had the pieces of code that I thought accomplished my goal the simplest.

Here's the code I included in my `.travis.yml` to have Hugo execute a build:

```yml
### Installing pre-requisites
install:
    - wget https://github.com/gohugoio/hugo/releases/download/v0.44/hugo_0.44_Linux-64bit.deb
    - sudo dpkg -i hugo*.deb

### Building website
script:
    - hugo
```

Note: The version of Hugo listed above was the latest stable version upon writing this post. To identify the latest version when you're configuring this I recommend you go [here](https://github.com/gohugoio/hugo/releases/).

### 3. Deployment of website to hosted S3 storage

When I was researching the method to accomplish this feature was when my eyes opened up to how robust Travis-CI is with what they offer pre-packaged with the tool for free.

The [Travis-CI Deployment Documentation](https://docs.travis-ci.com/user/deployment) speaks for itself. For this feature, I used the documentation found [here](https://docs.travis-ci.com/user/deployment/s3/).

To set this up, we'll have additional steps in the Travis-CI Web UI and the AWS IAM Console.

#### Code for Travis-CI: AWS S3

First things first, here's the necessary code for the `.travis.yml`:

```yml
### Sending newly generated website to S3 bucket
deploy:
  # Control deployment by setting a value for `on`. Setting the `branch`
  # option to `master` means Travis will only attempt a deployment on
  # builds of your repo's master branch (e.g., after you merge a PR).
  on:
    branch: master
  provider: s3
  # You can refer to environment variables from Travis repo settings!
  access_key_id: $AWS_ACCESS_KEY_ID
  secret_access_key: $AWS_SECRET_ACCESS_KEY
  # Name of the S3 bucket to which your site should be uploaded.
  bucket: $S3_BUCKET
  # Prevent Travis from deleting your built site so it can be uploaded.
  skip_cleanup: true
  # Path to a directory containing your built site.
  local_dir: public # Default build directory for Hugo
  # Set the Cache-Control header.
  cache_control: "max-age=21600"
```

#### Create AWS IAM user for Travis-CI

The reason for this step is for good security. The user we are about to create will have limited access to your AWS environment. If you desire, you could restrict the account even further by restricting it to only the S3 bucket and AWS CloudFront instance that you want it to interact with. For our purposes, I feel this is sufficient but to each his own.

* Navigate to the [AWS Identity & Access Management (IAM) portal](https://console.aws.amazon.com/iam/home#/users)
* Click **Add User**
  * Set **User name**. *ie. travis-ci*
  * For **Access type**, select **Programmatic access**
  * Click **Next: Permissions**
* Select **Attach existing policies directly**
  * Mark the following policies:
    * **AmazonS3FullAccess**
    * **CloudFrontFullAccess**
      * *This is for the next section.*
  * Click **Next: Review**
* Keep credentials on-screen for next step

#### Define Environment Variables within the Travis-CI Web UI: AWS S3

**Note:** You could define these variables straight in the `.travis.yml` but then you'd have to encrypt them following [this process](https://docs.travis-ci.com/user/environment-variables/#Defining-encrypted-variables-in-.travis.yml). Per Travis-CI docs, the only time it makes sense to put encrypted variables in the `.travis.yml`, is when the variable will change depending on the branch being committed.

* In the [Travis-CI Web UI](https://travis-ci.org/), edit the **Settings** for the GitHub repo with your Hugo source files.
* Add the following environment variables using these *exact* names:
  * **AWS_ACCESS_KEY_ID**
    * Created in previous step
  * **AWS_SECRET_ACCESS_KEY**
    * Created in previous step
  * **S3_BUCKET**
    * S3 bucket name that holds your static website

Note: Make sure to leave the Travis-CI environment variable on its default value for *Display value in build log*. ***NEVER*** display your AWS access key and secret key on the internet. Within an hour, you'll have people creating servers in your name in AWS! On that note, set an alert for yourself in AWS if your bill exceeds a certain amount. *This is a good idea for another blog article...*

### 4. CloudFront Invalidation (tells AWS to pull in the latest content)

In [Thom Greene's article](https://www.thomgreene.com/post/2018/2018-05-29-staticsitehugo/) that I referenced previously, he brought out the need to *invalidate* the CloudFront cache to force the latest content of your website to be re-cached. Without doing this, the CloudFront cache expires every 24 hours by default. The method he suggests is a simple AWS CLI command to do the deed.

```cli
aws cloudfront create-invalidation --distribution-id %distID% --paths "/*"
```

#### Identifying your CloudFront ID

Notice the variable `%distID%` being used in this command. This works fine when executed from my Mac, but does not work when executed in Travis-CI. This is not hard to overcome. You simply need to go to the [AWS CloudFront Console](https://console.aws.amazon.com/cloudfront/home?#) and capture the **ID** of your CloudFront instance.

#### Define Environment Variables within the Travis-CI Web UI: AWS CloudFront

* In the [Travis-CI Web UI](https://travis-ci.org/), edit the **Settings** for the GitHub repo with your Hugo source files.
* Add the following environment variables using these *exact* names:
  * **AWS_ACCESS_KEY_ID**
    * Should be already defined
  * **AWS_SECRET_ACCESS_KEY**
    * Should be already defined
  * **CLOUDFRONT_DISTRIBUTION_ID**
    * Captured info in previous step

#### Code for Travis-CI: AWS CloudFront

Here's the necessary code for the `.travis.yml`:

```yml
### Python is necessary for AWS CLI
language: python
python:
  - "3.6.5"

### Installing pre-requisites
install:
    - pip install awscli

### Forcing AWS CloudFront to refresh its cache
after_deploy:
  # Invalidate every object in the targeted distribution.
  - aws cloudfront create-invalidation --distribution-id $CLOUDFRONT_DISTRIBUTION_ID --paths "/*"
```

Looking at the code above, you'll see that Travis-CI is simply being instructed to install the AWS CLI. You might be wondering, *"but how is aws cli going to authenticate to my AWS environment?"* The answer is in the Travis-CI environment variables we created previously. The same ones we created for the S3 transfer, aws cli is able to use as well.

## Putting Everything Together

To make the code easier to understand, I piecemealed the `.travis.yml` thus far. Below you'll find the complete `.travis.yml` including all of the features that I've discussed in this post:

{{< highlight yml >}}

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
    - mdl -r ~MD002,~MD013,~MD033 content/ # rules MD013 & MD033 have been exluded
    - hugo


### Sending newly generated website to S3 bucket
deploy:
  # Control deployment by setting a value for `on`. Setting the `branch`
  # option to `master` means Travis will only attempt a deployment on
  # builds of your repo's master branch (e.g., after you merge a PR).
  on:
    branch: master
  provider: s3
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

### Forcing AWS CloudFront to refresh its cache
after_deploy:
  # Invalidate every object in the targeted distribution.
  - aws cloudfront create-invalidation --distribution-id $CLOUDFRONT_DISTRIBUTION_ID --paths "/*"
```

{{< /highlight >}}

One final point that I'll bring out is to pay attention to the procession of the code in the `.travis.yml`.

1. Define any special coding languages that need to be used.
2. Install pre-requisites
3. Run testing code
4. Deploy code to production
5. Define code to deploy afterwards

It's important to note that these steps are executed as a **whole**. For example, if the Markdown validation fails in step #3 - `mdl -r ~MD002,~MD013,~MD033 content/` - Travis-CI will continue to execute the `hugo` command as it's a part of step #3. It will not, however, continue to step #4 and will fail out the build.

## Feedback

I hope you find this post useful! I know it's been a learning experience for me as I've never used Travis-CI previously.

Please leave feedback below as it will be appreciated. If you have ideas for future posts that you'd like to see, please let me know and I'll look into it.