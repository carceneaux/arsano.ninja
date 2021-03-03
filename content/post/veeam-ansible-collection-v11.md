+++
title = "Veeam Ansible Collection"
description = "v11 Support"
tags = [
    "veeam",
    "ansible",
    "11",
    "v11",
    "collection",
    "role",
    "automation",
    "vbr",
    "em",
    "one"
]
date = "2021-03-03"
categories = [
    "Post"
]
+++

Veeam Backup & Replication v11 officially launched last week! With a new version of the product that is jam-packed with features, it's no wonder that it's already passed 15,000 unique downloads! Even before it was released, I was already being asked the question, when will the [Veeam Collection for Ansible](https://github.com/VeeamHub/veeam-ansible) support v11?

The answer is...right now! The [collection](https://github.com/VeeamHub/veeam-ansible) now fully supports all major components of the Veeam Availablility Suite:

* Veeam Backup & Replication
* Veeam Backup Enterprise Manager
* Veeam ONE

To the credit of the Veeam development team, there wasn't much I had to do on my end for this. As a matter of fact, new installs of VBR/EM worked with no adjustments required!

So what changes were required? Don't worry, I'll cover these in the remainder of this post...

## Multi-version Support

Probably the most notable change you'll see when reviewing the [sample playbooks](https://github.com/VeeamHub/veeam-ansible/tree/master/roles/veeam_vas#example-playbooks) in the documentation is that every playbook now contains the `version` parameter. This was done to make it very clear what version the playbook was being installed or upgraded to.

*If the `version` parameter isn't specified, the default of `"11"` will be used.*

Another benefit to this approach is I added an additional check that makes sure the ISO of the product being used matches the version you wanted in the playbook. This came about after I used the wrong version ISO...several different times... 😊

## Veeam Backup & Replication / Enterprise Manager

### Switch from PowerShell SnapIn to Module

This was a rather simply fix. Anywhere I previous added the Veeam SnapIn, I replaced it with code to check if the Veeam Module was available. This dynamic selection of the two allowed me to maintain backwards compatiblity for previous versions (<v11).

```powershell
if (-Not (Get-Module -ListAvailable -Name Veeam.Backup.PowerShell)){
    Add-PSSnapin -PassThru VeeamPSSnapIn -ErrorAction Stop | Out-Null
}
```

### Veeam Agent for MacOS Resdistributable

As this is a net new product, it wasn't included in my previously automated install/upgrade process. This was simple enough to add...

```yml
- name: Install Veeam Agent for MacOS Redistributable
  ansible.windows.win_package:
    path: "{{ source }}Packages\\VAMRedist.msi"
    state: present
    arguments: '/L*v "{{ destination }}logs\\VAMRedist.log" /norestart /qn ACCEPTEULA="YES" ACCEPT_THIRDPARTY_LICENSES="1"'
  when: version | int >= 11
```

## Veeam ONE

### New Software Prerequisites

A new and improved UI in Veeam ONE meant different required software to be installed prior to kicking off the Veeam ONE install. Here's a list of the new prerequisites:

* Microsoft OLE DB Driver for SQL Server
* Microsoft .NET Core Runtime
* Microsoft ASP.NET Core Shared Framework
* Microsoft Visual C++ Redistributable
* Microsoft Application Request Routing
* IIS URL Rewrite Module

## Final thoughts

On top of the changes made here, I also made minor changes throughout the collection improving verbiage to make things more clear and improving the overall flow of the collection.

If you have questions or feedback regarding the collection, feel free to [create an issue](https://github.com/VeeamHub/veeam-ansible/issues/new/choose) in GitHub.
