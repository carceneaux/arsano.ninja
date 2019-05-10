+++
title = "GitLab CI Artifact Retention"
description = "Discussion on proper handling of GitLab CI Job Artifacts."
tags = [
    "gitlab",
    "gitlab ci",
    "ci/cd",
    "bash",
    "rest api",
    "job",
    "artifact",
    "artifact deletion",
    "artifact retention"
]
date = "2019-05-09"
categories = [
    "Post"
]
+++

A few months ago the disk space on my on-prem GitLab instance began to fill up. Fortunately for me, my monitoring reported the issue to me before it became a major problem. My first thought was, _why is this happening?_ I knew that I had provisioned more than enough space and there should be no reason it was almost full.

After a brief investigation, I found that the _build artifacts_ section of most of my repositories was 99% of the total repository size...some of them being over 40GB in size! That's when I went down the rabbit hole of artifact retention and artifact deletion. In this post, we'll focus on the _solution_ to this problem as well as a _stop-gap_.

## Solution

### Set Global Artifact Retention Policy _(On-Prem Only)_

The default [GitLab Global Policies](https://docs.gitlab.com/ee/user/gitlab_com/index.html#gitlab-cicd) for artifact expiration differ between GitLab.com and on-prem GitLab versions. Depending on your GitLab CI workloads, pipeline size, and job runtimes you'll want to set the expiration time that works best for your environment. Always err on the side of larger for your global setting as you don't want to negatively affect a job run because the artifact was deleted too soon.

| **Setting** | **GitLab.com** | **On-Prem GitLab Versions** |
|---|---|---|
| Artifacts [expiry time](https://docs.gitlab.com/ee/ci/yaml/README.html#artifactsexpire_in) | kept forever | 30 days |

### Set Artifact Retention in _.gitlab-ci.yml_

Starting with GitLab 8.9 artifact expiration within the `.gitlab-ci.yml` became possible. This becomes a necessity if you're using GitLab.com as you're unable to set the global artifact expiration policy. It also gives you finer control in on-prem GitLab instances as it allows you to delete an artifact prior to when the global retention policy comes into effect.

[GitLab Documentation on the `artifacts:expire_in` setting.](https://docs.gitlab.com/ee/ci/yaml/#artifactsexpire_in)

**Sample:**

```yml
job:
  artifacts:
    expire_in: 1 week
```

## Stop-Gap