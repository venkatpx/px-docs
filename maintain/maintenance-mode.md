---
layout: page
title: Maintenance Mode
keywords: 'maintenance, drive removal, drive replacement'
sidebar: home_sidebar
redirect_from: /maintenance.html
---

# maintenance-mode

* TOC

  {:toc}

Maintenance Mode is useful for cases where a node needs to be physically decommissioned. The most common cases would be for Disk/DIMM/NIC replacement.

Here are some of the commands that are needed for maintenance operations

## Some general maintenance commands

### Enter Maintenance Mode

Run **"pxctl service maintenance --enter"**. This takes Portworx out of an "Operational" state for a given node. Perform whatever physical maintenance is needed.

### Restart Portworx

Run **"docker restart px-enterprise"**. This restarts the Portworx fabric on a given node.

### Exit Maintenance Mode

Run **"pxctl service maintenance --exit"**. This puts Portworx back in to "Operational" state for a given node.

### Drive management example

The drive management commands are organized under `pxctl service drive` command

```text
/opt/pwx/bin/pxctl service drive

NAME:
   pxctl service drive - Storage drive maintenance

USAGE:
   pxctl service drive command [command options] [arguments...]

COMMANDS:
     show           Show drives
     add            Add storage
     replace        Replace source drive with target drive
     rebalance, rs  Rebalance storage

OPTIONS:
   --help, -h  show help
```

Here is a typical workflow on how to identify and replace drives.

## Show the list of drives in the system

```text
/opt/pwx/bin/pxctl service drive show

PX drive configuration:
Pool ID: 0
    IO_Priority: LOW
    Size: 7.3 TiB
    Status: Online
    Has meta data: No
    Drives:
    1: /dev/sde, 3.0 GiB allocated of 7.3 TiB, Online
Pool ID: 1
    IO_Priority: HIGH
    Size: 1.7 TiB
    Status: Online
    Has meta data: Yes
    Drives:
    1: /dev/sdj, 1.0 GiB allocated of 1.7 TiB, Online
```

## Add drives to the cluster

### Step 1: Enter Maintenance Mode

```text
/opt/pwx/bin/pxctl service  maintenance --enter
This is a disruptive operation, PX will restart in maintenance mode.
Are you sure you want to proceed ? (Y/N): y
PX is not running on this host.
```

### Step 2: Add drive to the system

For e.g., Add drive /dev/sdb to PX cluster

```text
/opt/pwx/bin/pxctl service drive add /dev/sdb
Adding device  /dev/sdb ...
Drive add  successful. Requires restart (Exit maintenance mode).
```

### Step 3: Exit Maintenance mode

```text
/opt/pwx/bin/pxctl service  maintenance --exit
PX is now operational
```

Check if the drive is added using drive show command

```text
/opt/pwx/bin/pxctl service drive show
PX drive configuration:

Pool ID: 0
    IO_Priority: LOW
    Size: 15 TiB
    Status: Online
    Has meta data: No
    Drives:
    2: /dev/sdb, 0 B allocated of 7.3 TiB, Online
    1: /dev/sde, 3.0 GiB allocated of 7.3 TiB, Online
Pool ID: 1
    IO_Priority: HIGH
    Size: 1.7 TiB
    Status: Online
    Has meta data: Yes
    Drives:
    1: /dev/sdj, 1.0 GiB allocated of 1.7 TiB, Online
```

## Replace a drive that is already part of the Portworx Cluster

### Step 1: Enter Maintenance mode

```text
/opt/pwx/bin/pxctl service  maintenance --enter
This is a disruptive operation, PX will restart in maintenance mode.
Are you sure you want to proceed ? (Y/N): y

PX is not running on this host.
```

### Step 2: Replace old drive with a new drive

Ensure the replacement drive is already available in the system.

For e.g., Replace drive /dev/sde with /dev/sdc

```text
/opt/pwx/bin/pxctl service drive replace --source /dev/sde --target /dev/sdc --operation start
"Replace operation is in progress"
```

Check the replace status

```text
/opt/pwx/bin/pxctl service drive replace --source /dev/sde --target /dev/sdc --operation status
"Started on 16.Dec 22:17:06, finished on 16.Dec 22:17:06, 0 write errs, 0 uncorr. read errs\n"
```

### Step 3: Exit Maintenance mode

```text
/opt/pwx/bin/pxctl service  maintenance --exit
PX is now operational
```

### Step 4: Check if the drive has been successfully replaced

```text
/opt/pwx/bin/pxctl service drive show
PX drive configuration:
Pool ID: 0
    IO_Priority: LOW
    Size: 15 TiB
    Status: Online
    Has meta data: No
    Drives:
    1: /dev/sdc, 3.0 GiB allocated of 7.3 TiB, Online
    2: /dev/sdb, 0 B allocated of 7.3 TiB, Online
Pool ID: 1
    IO_Priority: HIGH
    Size: 1.7 TiB
    Status: Online
    Has meta data: Yes
    Drives:
    1: /dev/sdj, 1.0 GiB allocated of 1.7 TiB, Online
```

