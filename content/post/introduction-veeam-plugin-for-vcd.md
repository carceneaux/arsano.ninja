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
date = "2019-09-26"
categories = [
    "Post"
]
+++

Almost 4 years ago, Veeam released the [vCloud Director (vCD) Self Service Portal](https://helpcenter.veeam.com/docs/backup/em/em_managing_vms_in_vcd_org.html?ver=95u4). The portal leverages Enterprise Manager and allows vCD Tenants to fully administer backups for their vCD workloads. Since then, Veeam has steadily improved this portal.

While Veeam made consumption of the Self Service Portal as easy as possible, it was still a separate portal outside of vCD. Early last year, vCloud Director 9.1 released the [vCD Extensibility Framework](https://github.com/vmware/vcd-ext-sdk). This new functionality was a game changer as it provided a method for making custom portals accessible within vCD!

Since that time, I've wanted to make the Veeam Self Service Portal accessible within vCloud Director. I'm happy to report that today I'm releasing the [Veeam Plugin for vCD](https://test.arsano.ninja)!

Documentation for deployment & configuration of this plugin can be found [here](https://test.arsano.ninja).

Here's a video demonstrating the plugin configuration & usage:

{{< youtube ScMzIvxBSi4 >}}
