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

Almost 4 years ago, Veeam released the [vCloud Director Self Service Tenant Portal (VSSP)](https://helpcenter.veeam.com/docs/backup/em/em_managing_vms_in_vcd_org.html?ver=95u4). This portal allows vCloud Director (vCD) Tenants the ability to fully administer their backups. Since then, Veeam has steadily improved this portal.

Veeam made consumption of the VSSP as easy as possible by using vCD organization administrator credentials for auth. Even so, the VSSP was still a separate portal outside of vCloud Director. Early last year, vCloud Director 9.1 released the [vCloud Director Extensibility Framework](https://github.com/vmware/vcd-ext-sdk). That new functionality was a game changer as it provided a method for making custom portals accessible within vCD!

Since that time, I've wanted to make the VSSP accessible within vCloud Director. I'm happy to report that today I'm releasing the [Veeam Plugin for vCD](https://test.arsano.ninja)!

Documentation for deployment & configuration of this plugin can be found [here](https://test.arsano.ninja).

Here's a video demonstrating the plugin configuration & usage:

{{< youtube ScMzIvxBSi4 >}}
