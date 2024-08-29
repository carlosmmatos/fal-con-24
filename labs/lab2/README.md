# Lab 2: Combatting Shadow IT with Dynamic Inventories

## Overview

In this lab, you will learn how we can leverage the Falcon Discover API via the `crowdstrike.falcon.falcon_discover` Ansible
dynamic inventory to combat Shadow IT. This lab will demonstrate how to use the Falcon Discover dynamic inventory to identify all the assets in your environment, and how to use the filters and dynamic inventory features of Ansible to target specific assets for remediation. In our case, we will be deploying the Falcon sensor against unmanaged assets.

## Objectives

- Learn how to use the Falcon Discover Dynamic Inventory to identify assets in your environment
- Learn how to use Ansible filters to target specific assets
- Learn how to group assets using Ansible's dynamic inventory
- Learn how to modify host variables in Ansible's dynamic inventory
- Learn how to use Ansible to deploy the Falcon sensor against unmanaged assets

## Steps

1. Navigate to the ~/labs/lab2 directory

    ```bash
    cd ~/labs/lab2
    ```

1. Review the `demo.falcon_discover.yml` inventory file

    ```bash
    less demo.falcon_discover.yml
    ```

1. Run the `ansible-inventory` command to view the assets identified by the Falcon Discover dynamic inventory

    ```bash
    ansible-inventory -i demo.falcon_discover.yml --graph
    ```

1. Run the `ansible-inventory` command this time with the `--list` option to see a more detailed view of the assets identified by the Falcon Discover dynamic inventory

    ```bash
    ansible-inventory -i demo.falcon_discover.yml --list | less
    ```

### Filter Assets

#### View all AWS assets in our environment

Uncomment the following line in the `demo.falcon_discover.yml` file:

```yaml
# filter: "cloud_provider:'AWS'"
```

Save the file and run the `ansible-inventory` command to view the filtered assets

```bash
ansible-inventory -i demo.falcon_discover.yml --list | less
```

#### View all unmanaged assets in our environment

Recomment the previous line and uncomment the following line in the `demo.falcon_discover.yml` file:

```yaml
#filter: "entity_type:'unmanaged'+first_seen_timestamp:>'now-1d'"
```

Save the file and run the `ansible-inventory` command to view the filtered assets

```bash
ansible-inventory -i demo.falcon_discover.yml --list | less
```

#### View all the 'ansible' lab VMs in our environment

Recomment the previous line and uncomment the following line in the `demo.falcon_discover.yml` file:

```yaml
#filter: hostname:*'*-ansible'
```

Save the file and run the `ansible-inventory` command to view the filtered assets

```bash
ansible-inventory -i demo.falcon_discover.yml --list | less
```

### Grouping Assets

Now that we have seen how to filter assets, let's group them to make it easier to target specific assets.

> [!NOTE]
> Groups in dynamic inventories come in 2 different flavors: `keyed_groups` and `groups`. `keyed_groups` are groups that are based on a key in the host's variables, while `groups` are groups that are based on a filter. In this lab, we'll explore both.

#### Keyed Groups

##### Group assets by `cloud_provider`

Under the `keyed_groups` section in the `demo.falcon_discover.yml` file, uncomment the following lines:

```yaml
  #- prefix: cloud
  #  key: cloud_provider
```

Save the file and run the `ansible-inventory` command to view the grouped assets

```bash
ansible-inventory -i demo.falcon_discover.yml --graph
```

#### Groups

##### Group assets by `entity_type`

Under the `groups` section in the `demo.falcon_discover.yml` file, uncomment the following lines:

```yaml
  #unmanaged_assets: "entity_type == 'unmanaged'"
```

This creates a group called `unmanaged_assets` that contains all the unmanaged assets in our environment. Save the file and run the `ansible-inventory` command to view the grouped assets

```bash
ansible-inventory -i demo.falcon_discover.yml --graph
```

### Modify Host Variables
