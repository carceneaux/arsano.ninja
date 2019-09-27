+++
title = "First Look: Veeam Plugin for vCD "
description = "An introduction to the new Veeam plugin"
tags = [
    "vcd",
    "vmware vcloud director",
    "vcloud director",
    "veeam",
    "vssp",
    "vcloud director self service tenant portal",
    "self-service",
    "vcd plugin",
    "vcd extenstion",
    "veeam plugin for vcd",
    "veeam extension for vcd"
]
date = "2019-09-27"
categories = [
    "Post"
]
+++

Veeam Backup & Replication version 9.5 released the [vCloud Director (vCD) Self Service Portal](https://helpcenter.veeam.com/docs/backup/em/em_managing_vms_in_vcd_org.html?ver=95u4) that leverages Enterprise Manager and allows vCD Tenants to fully administer backups for their vCD workloads. Upon release, Veeam made consumption of this Self Service Portal as easy as possible as it leverages vCD authentication for tenants so they don't have to manage two sets of credentials. Even so, it was still a separate portal independent of vCD.

Early last year, vCloud Director 9.1 released the [vCD Extensibility Framework](https://github.com/vmware/vcd-ext-sdk). This new functionality is a game changer! It provided an avenue for developing custom portals to be accessible within vCD.

Since then, I've wanted to make the Veeam Self Service Portal utilize this framework for added vCD goodness. 😊 I'm happy to report that today I'm releasing the [Veeam Plugin for vCD](https://test.arsano.ninja)!

[Documentation for deployment & configuration of this plugin can be found here.](https://test.arsano.ninja)

_Note: This new plugin does not change the Architecture & Design of the Veeam Self Service Portal. It serves only as a medium to easily present the Self Service Portal within vCD._

Check out the new plugin in action:

{{< youtube By4G_WH9k40 >}}
