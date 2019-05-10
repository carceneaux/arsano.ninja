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

_**Note:** If you're unfamiliar with GitLab CI, I highly recommend you try it out on GitLab.com for free._

A few months ago, the disk space on my on-prem GitLab instance began to fill up. Fortunately for me, my monitoring reported the issue before it became a major problem. My first thought was, _why is this happening?_ I knew that I had provisioned more than enough space and there should be no reason it was almost full.

After a brief investigation, I found that the _build artifacts_ section of most of my repositories was 99% of the total repository size...some of them being over 40GB! That's when I went down the rabbit hole of artifact retention and artifact deletion. In this post, we'll focus on the _solution_ to this problem as well as a _stop-gap_.

## Solution

### Set Global Artifact Retention Policy _(On-Prem Only)_

The default [GitLab Global Policies](https://docs.gitlab.com/ee/user/gitlab_com/index.html#gitlab-cicd) for artifact expiration differ between GitLab.com and on-prem GitLab versions. Depending on your GitLab CI workloads, pipeline size, job runtimes, and _disk size_ 😊, you'll want to set the expiration time that works best for your environment. Always err on the side of larger for your global setting as you don't want to negatively affect a job run because the artifact was deleted too soon.

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

Now that I've discussed proper handling of Job Artifacts, let's move on to the stop-gap... It's all fine and dandy to know about proper artifact retention settings but what do I do with the artifacts that were created previously? GitLab provides two methods for deleting artifacts that have no expiration:

* GitLab Web UI
* GitLab REST API

Deleting artifacts via the Web UI is a non-starter IMO unless you want to delete only a few. That leaves the best option to be leveraging the REST API. In my searching, I came upon a very helpful [GitLab forum post](https://forum.gitlab.com/t/remove-all-artifact-no-expire-options/9274/8). This served as the base for the script I used to clean up my artifacts. I hope this serves you well.

### Bash Script for removing GitLab Job Artifacts

[Script Source Code](https://gist.github.com/carceneaux/b75d483e3e0cb798ae60c424300d5a0b)

Prior to running the script, make sure you have `curl` and `jq` installed and you've updated the `project_id`, `token`, and `server` variables to match your environment.