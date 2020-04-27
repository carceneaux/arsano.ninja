+++
title = "Veeam Ansible Collection"
description = "Administer your Veeam environment Infrastructure as Code"
tags = [
    "veeam",
    "ansible",
    "collection",
    "role",
    "automation",
    "vbr",
    "em",
    "one"
]
date = "2020-04-27"
categories = [
    "Post"
]
+++

After completing my PowerShell script recently to [upgrade Veeam Backup & Replication environments to v10](https://github.com/VeeamHub/powershell/tree/master/BR-UpgradeV10), I wanted to take the next step and automate the full lifecycle process. I've had Ansible in my sights for a while to do this as it can natively use PowerShell so I took the plunge. [Markus Kraus](https://twitter.com/vMarkus_K), a Veeam VanGaurd, started work along this same train of thought last year and this year updated the code to support [Veeam Backup & Replication v10](https://mycloudrevolution.com/en/2020/02/05/veeam-availability-suite-10-unattended-installation/).

As this was my first Ansible role to write, it was very helpful to use Markus's code as a base for my project. Here are the capabilities of this release:

* Automated *Install/Patch/Upgrade* operations for all products in the [Veeam Availability Suite](https://www.veeam.com/data-center-availability-suite.html)
* Versions supported for *Install/Patch*:
  * 9.5 Update 4
  * 10
* Versions supported for *Upgrade*:
  * Upgrading to v10 from supported source versions
* Pre-requisite software is installed prior to *Install/Upgrade*
* Central location for all *Install/Patch/Upgrade* logs
* Automated patching for *Install/Upgrade*
  * Checks for *Updates* folder in ISO and installs patches found
* Veeam Backup & Replication
  * Configuration Backup is performed prior to *Patch/Upgrade*
  * Cloud Connect environments are supported
  * Disables jobs prior to *Patch/Upgrade*
  * Enables jobs prior to *Patch/Upgrade*
* Veeam Backup Enterprise Manager
  * SQL DB Backup is performed prior to *Patch/Upgrade*
  * Cloud Connect environments are supported
* Veeam ONE
  * SQL DB Backup is performed prior to *Patch/Upgrade*
  * [Advanced Deployment](https://helpcenter.veeam.com/docs/one/deployment/advanced_deployment.html?ver=100) architecture supported

The source code for this project can be found in [VeeamHub](https://github.com/VeeamHub/veeam-ansible). Downloading & using the code is super easy with the use of [Ansible Galaxy](https://galaxy.ansible.com/veeamhub/veeam).

This code was tested against:

* Windows Server 2019
* Windows Server 2016 (64-bit)

If you have any questions/problems/feature requests about this Ansible collection, please use the projects [GitHub Issue Tracker](https://github.com/VeeamHub/veeam-ansible/issues/new/choose).
