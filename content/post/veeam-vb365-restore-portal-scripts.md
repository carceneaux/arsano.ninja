+++
title = "Veeam Backup for Microsoft 365 v6"
description = "Automating Restore Portal Configuration"
tags = [
    "veeam",
    "microsoft 365",
    "v6",
    "powershell",
    "scripts",
    "vbo",
    "vb365",
    "multi-tenant"
]
date = "2022-03-23"
categories = [
    "Post"
]
+++

After completing my PowerShell script to [upgrade Veeam Backup & Replication environments to v10](https://github.com/VeeamHub/powershell/tree/master/BR-UpgradeV10), I wanted to take the next step and automate the full lifecycle process. I've had Ansible in my sights for a while to do this as it can natively use PowerShell.

[Markus Kraus](https://twitter.com/vMarkus_K), a Veeam VanGaurd, started work along this same train of thought last year and recently updated the code to support [Veeam Backup & Replication v10](https://mycloudrevolution.com/en/2020/02/05/veeam-availability-suite-10-unattended-installation/). As this was my first Ansible role to write, it was very helpful to use Markus's code as a base for my project.

## Capabilities of this release

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
  * Enables jobs after *Patch/Upgrade*
* Veeam Backup Enterprise Manager
  * SQL DB Backup is performed prior to *Patch/Upgrade*
  * Cloud Connect environments are supported
* Veeam ONE
  * SQL DB Backup is performed prior to *Patch/Upgrade*
  * [Advanced Deployment](https://helpcenter.veeam.com/docs/one/deployment/advanced_deployment.html?ver=100) architecture supported

The source code for this project can be found in [VeeamHub](https://github.com/VeeamHub/veeam-ansible). Downloading & using the code is super easy with the use of [Ansible Galaxy](https://galaxy.ansible.com/veeamhub/veeam).

If you've never used Ansible previously, I recommend getting your feet wet with the [Ansible Getting Started Guide](https://docs.ansible.com/ansible/latest/network/getting_started/first_playbook.html). It does a really good job of explaining what Ansible is and how it works. The part I like most about Ansible is it doesn't require an agent to be installed on managed systems as it uses remote connection methods like SSH & WinRM instead.

Ansible playbooks are used to execute complex instructions in a simplistic manner. Below is a sample playbook to install Veeam Backup & Replication v10:

```yml
- name: Veeam Backup & Replication v10 Install
  hosts: veeam
  tasks:
    - include_role:
        name: veeamhub.veeam.veeam_vas
        tasks_from: vbr_install
      vars:
        iso_download: true
        license: true
        source_license: "/root/ansible/license.lic"
        sql_install_username: "sql_install" #used to install SQL Express (admin)
        sql_install_password: "ChangeM3!"
        sql_service_username: "svc_sql" #used to run SQL Express (non-admin)
        sql_service_password: "ChangeM3!"
        sql_username: "sa" #used by Veeam to connect to SQL Express
        sql_password: "ChangeM3!"
        # https://docs.ansible.com/ansible/latest/user_guide/playbooks_vault.html#single-encrypted-variable
```

Included with the documentation on VeeamHub, I've provided plenty of [sample playbooks](https://github.com/VeeamHub/veeam-ansible/tree/master/roles/veeam_vas#example-playbooks).

![Ansible VBR 10 Install]({{< siteurl >}}images/ansible-vbr-install.png)

## Contributing

If you'd like to support this community project, I welcome contributions from the community. For more information, feel free to reference the [project contributing guide](https://github.com/VeeamHub/veeam-ansible/blob/master/CONTRIBUTING.md).

## Next Steps

I'm currently working on feature enhancements for this Ansible collection. Currently, I have in mind to add support for performing basic Veeam Backup & Replication configuration tasks:

* Configuration Backup settings
* Add/Remove Veeam Managed Servers
* Add/Remove Veeam Backup Proxy
* Add/Remove Veeam Backup Repository
* Add/Remove VMware vCenter
* Add/Remove VMware vCloud Director
* More to come!

If you have any questions/problems/feature requests about this Ansible collection, please use the projects [GitHub Issue Tracker](https://github.com/VeeamHub/veeam-ansible/issues/new/choose).
