+++
title = "Automated Upgrade for Veeam v11"
description = "Unattended Install Notes"
tags = [
    "veeam",
    "powershell",
    "11",
    "v11",
    "automation",
    "unattended",
    "upgrade",
    "vbr",
    "em"
]
date = "2021-03-09"
categories = [
    "Post"
]
+++

After the release of Veeam Backup & Replication v10 last year, I wrote a [PowerShell script to automate the upgrade](https://github.com/VeeamHub/powershell/tree/master/BR-UpgradeV10). It's as easy as they come as it enabled one-click upgrades of Veeam environments.

With the release of v11, I wanted to continue the tradition to enable those with multiple Veeam servers, the *easy* button for mass upgrades to the latest version of the product. I'm happy to release an [updated version](https://github.com/VeeamHub/powershell/tree/master/BR-UpgradeV11) of this script for v11!

[Automated Upgrade to Veeam Backup & Replication v11](https://github.com/VeeamHub/powershell/tree/master/BR-UpgradeV11)

This script supports automated upgrade for the following products:

* Veeam Backup & Replication
* Veeam Backup Enterprise Manager

***Includes support for Veeam Cloud Connect environments!***

## Changes in the upgrade process v10 to v11

Kudos to the Veeam development team as the upgrade process between these two versions was nearly identical! Please see below for a detailed list on what changes I made to this script to support v11:

### PowerShell SnapIn to Module

This enabled support for the latest versions of PowerShell. While PowerShell Core is still not fully supported, this is definitely a step in the right direction.

Key thing to note for the new Veeam Module is there is **no longer any need to import the module in your PowerShell scripts**. The first time you execute a Veeam cmdlet, the module will auto-import itself into your PowerShell session.

### Veeam Agent for MacOS Redistributable

With the arrival of this new product, this mark a net new package to be installed during the upgrade.

## Final thoughts

On top of the changes listed above, I also made minor changes throughout the script to improve compatibility with Server 2016 and error handling as sometimes *msiexec* returned other codes than `0` for successful installs.

If you have questions or feedback regarding this script, feel free to [create an issue](https://github.com/VeeamHub/powershell/issues/new/choose) in GitHub.
