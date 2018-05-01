---
layout: page
title: Using Storage Classes
keywords: >-
  portworx, storage class, container, Kubernetes, storage, Docker, k8s, flexvol,
  pv, persistent disk
sidebar: home_sidebar
---

# Storage Classes

Using [Storage Class](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#class-1) objects an admin can define the different classes of Portworx volumes that are offered in a cluster. Following are the different parameters that can be used to define a Portworx Storage Class:

```text
- fs: filesystem to be laid out: none|xfs|ext4 (default: `ext4`)
- block_size: block size in Kbytes (default: `32`)
- repl: replication factor [1..3] (default: `1`)
- io_priority: IO Priority: [high|medium|low] (default: `low`)
- snap_interval: snapshot interval in minutes, 0 disables snaps (default: `0`)
- aggregation_level: specifies the number of replication sets the volume can be aggregated from (default: `1`)
- ephemeral: ephemeral storage [true|false] (default `false`)
- parent: a label or name of a volume or snapshot from which this storage class is to be created
- secure: to create an encrypted storage class
```

## Step1: Create Storage Class.

Create the storageclass:

```text
# kubectl create -f \
   examples/volumes/portworx/portworx-volume-sc-high.yaml
```

Example:

```yaml
     kind: StorageClass
     apiVersion: storage.k8s.io/v1beta1
     metadata:
       name: portworx-io-priority-high
     provisioner: kubernetes.io/portworx-volume
     parameters:
       repl: "1"
       snap_interval:   "70"
       io_priority:  "high"
```

[Download example](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/k8s-samples/portworx-volume-sc-high.yaml?raw=true)

Verifying storage class is created:

```text
# kubectl describe storageclass portworx-io-priority-high
     Name:                 portworx-io-priority-high
     IsDefaultClass:            No
     Annotations:        <none>
     Provisioner:        kubernetes.io/portworx-volume
     Parameters:        io_priority=high,repl=1,snapshot_interval=70
     No events.
```

