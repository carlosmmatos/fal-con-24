# Lab 4: Modules, Plugins, and more

## Overview

In this lab, you will learn how to leverage the CrowdStrike Falcon modules and plugins for Ansible  within your environment, moving outside of traditional sensor deployment.

## Objectives

By the end of this lab, you will be able to:

- Understand where to find the documentation for the CrowdStrike Falcon modules and plugins
- Understand the different CrowdStrike Falcon modules and plugins for Ansible
- Use the CrowdStrike Falcon modules and plugins to automate tasks

## Steps

1. Navigate to the `labs/lab4` directory to begin the lab.

    ```bash
    cd ~/labs/lab4
    ```

1. Review the structure of the lab directory by typing the `tree` command

    ```terminal
    .
    ├── README.md
    ├── ansible.cfg
    ├── examples
    │   ├── demo.falcon_hosts.yml
    │   ├── host-hide-inventory.yml
    │   └── host-hide-lookup.yml
    ├── inventory
    ├── modules
    │   ├── auth.yml
    │   ├── kernel_support_info1.yml
    │   ├── kernel_support_info2.yml
    │   ├── sensor_download.yml
    │   └── sensor_download_info.yml
    └── plugins
        ├── host_ids.yml
        └── maintenance_token.yml
    ```

The lab directory is broken out into the following sections:

- **modules**: Contains example playbooks demonstrating the use of the CrowdStrike Falcon modules.
- **plugins**: Contains example playbooks demonstrating the use of the CrowdStrike Falcon plugins.
- **examples**: Contains example playbooks demonstrating ways to take advantage of the CrowdStrike Falcon modules and plugins.

The inventory file contains a static inventory for the lab environment that consists of:

- `localhost` - The local machine running the Ansible playbook
- `sketchy_cats` - A group that contains your two `sketchy-cat` hosts

### Documentation

Before we dive into the modules and plugins, let's take a look at the documentation for the CrowdStrike Falcon modules, plugins, and other resources.

#### GitHub Repository

The CrowdStrike Falcon Ansible collection is available on GitHub at https://github.com/CrowdStrike/ansible_collection_falcon

This is the primary source of truth for the CrowdStrike Falcon Ansible collection. You can find the latest documentation, release notes, and other resources here.

#### Ansible Galaxy

The CrowdStrike Falcon Ansible collection is also available on Ansible Galaxy at https://galaxy.ansible.com/ui/repo/published/crowdstrike/falcon

#### Red Hat Ansible Automation Hub

As a certified Ansible collection, the CrowdStrike Falcon Ansible collection is also available on Automation Hub at https://console.redhat.com/ansible/automation-hub/repo/published/crowdstrike/falcon/

#### CLI via `ansible-doc`

You can also use the `ansible-doc` command to view the documentation for the CrowdStrike Falcon modules and plugins.

To list all the available modules, lookup plugins, and roles in the collection by type, use the following commands:

```bash
ansible-doc -l crowdstrike.falcon -t module
ansible-doc -l crowdstrike.falcon -t lookup
ansible-doc -l crowdstrike.falcon -t role
```

> [!NOTE]
> :disappointed:
> Unfortunately there is no way to list ALL the plugins in the collection at once. You will need to list them by type.

To see the documentation for the crowdstrike.falcon.sensor_download module:

```bash
ansible-doc crowdstrike.falcon.sensor_download
```

> For more information on the `ansible-doc` command, see the [Ansible documentation](https://docs.ansible.com/ansible/latest/cli/ansible-doc.html).

### Explore the CrowdStrike Falcon Modules

> [!NOTE]
> The following examples are just to demonstrate a subset of the CrowdStrike Falcon modules available in the collection. For a complete list of modules, see any of the documentation sources mentioned above.

#### Auth Module

Manage token authentication for the CrowdStrike Falcon API.

Review the `auth.yml` module playbook in the `modules` directory.

```bash
cat modules/auth.yml
```

Execute the `auth.yml` module playbook.

```bash
ansible-playbook modules/auth.yml -v
```

#### Kernel Support Info Module

Get information about kernels supported by the Falcon Sensor for Linux. This module is useful for determining if the Falcon Sensor is compatible with the kernel version running on a host.

> [!IMPORTANT]
> This does not reflect systems running in User Mode.

Review the `kernel_support_info1.yml` module playbook in the `modules` directory.

```bash
cat modules/kernel_support_info1.yml
```

Execute the `kernel_support_info1.yml` module playbook.

```bash
ansible-playbook modules/kernel_support_info1.yml -v | tee output.txt
```

Review the `kernel_support_info2.yml` module playbook in the `modules` directory.

```bash
cat modules/kernel_support_info2.yml
```

Execute the `kernel_support_info2.yml` module playbook.

> [!TIP]
> You can use Ansible's Jinja2 filters to further refine the output.
> For example, you can use the `selectattr` filter to filter the output based on a specific attribute.

```bash
ansible-playbook modules/kernel_support_info2.yml | tee output.txt
```

#### Sensor Download Info Module

Get information about available Falcon Sensor Installers.

Review the `sensor_download_info.yml` module playbook in the `modules` directory.

```bash
cat modules/sensor_download_info.yml
```

Execute the `sensor_download_info.yml` module playbook.

```bash
ansible-playbook modules/sensor_download_info.yml | tee output.txt
```

#### Sensor Download Module

Download the Falcon Sensor Installer. This module downloads the Falcon Sensor Installer to the **local** machine.

> [!IMPORTANT]
> This module will not install the Falcon Sensor. It will only download the installer.
>
> To install the Falcon sensor, use the `crowdstrike.falcon.falcon_install` role.

Review the `sensor_download.yml` module playbook in the `modules` directory.

```bash
cat modules/sensor_download.yml
```

> [!NOTE]
> Notice how we are using the `crowdstrike.falcon.sensor_download_info` module to get a list of available sensor installers before downloading the installer.
>
> Also note the `n_minus` variable is used in this playbook to download a specific N-# version of the sensor installer.

### Explore the CrowdStrike Falcon Plugins

> [!NOTE]
> There is a distinction between modules and plugins in Ansible. Modules are standalone scripts that can be used by Ansible to automate tasks. Plugins are pieces of code that extend Ansible's core functionality. Plugins can be lookups, filters, callbacks, inventories, etc.
>
> Currently, the CrowdStrike Falcon Ansible collection contains lookup and inventory plugins.

#### Host IDs Lookup Filter Plugin

Fetch host IDs. This plugin is extremely useful as input to the other **host** modules.

Review the `host_ids.yml` plugin playbook in the `plugins` directory.

```bash
cat plugins/host_ids.yml
```

Execute the `host_ids.yml` plugin playbook.

```bash
ansible-playbook plugins/host_ids.yml | tee output.txt
```

#### Maintenance Token Lookup Filter Plugin

Fetch the maintenance token. This plugin will fetch the maintenance token for specified hosts, or it can also retrieve the bulk maintenance token (*if applicable in your environment*).

>Maintenance tokens are used to uninstall the Falcon Sensor from a host.

Review the `maintenance_token.yml` plugin playbook in the `plugins` directory.

```bash
cat plugins/maintenance_token.yml
```

> [!TIP]
> You can use the `crowdstrike.falcon.host_ids` lookup plugin to get the host IDs for the hosts you want to retrieve the maintenance token for.

Execute the `maintenance_token.yml` plugin playbook.

```bash
ansible-playbook plugins/maintenance_token.yml | tee output.txt
```

#### Falcon Hosts Inventory Plugin

This dynamic inventory queries asset details from the CrowdStrike Falcon Hosts API.

> [!NOTE]
> The difference between this inventory plugin and the `crowdstrike.falcon.falcon_discover` inventory plugin is that this plugin will only work with **managed** assets that already have the sensor installed.

Review the `demo.falcon_hosts.yml` inventory playbook in the `plugins` directory.

```bash
vim plugins/demo.falcon_hosts.yml
```

Run the `ansible-inventory` command to see the inventory generated by the `demo.falcon_hosts.yml` inventory plugin.

```bash
ansible-inventory -i plugins/demo.falcon_hosts.yml --list -l sketchy | tee output.txt
```

### Example Playbooks

#### Example 1: Hiding hosts from the Falcon console

##### Using lookup plugin

##### Using a dynamic inventory

#### Example 2: ?? Maybe CVE stuff?
