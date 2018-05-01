---
layout: page
title: Run Portworx and 'etcd' on Marathon
keywords: 'portworx, PX-Developer, container, Mesos, Mesosphere, Marathon, storage'
sidebar: home_sidebar
---

# px\_etcd\_marathon

Portworx version through 1.2 depend on an external key-value datastore \(e.g. etcd or consul\) for cluster topology. To simplify deployment of Mesosphere/DCOS for these environments, the following converged Marathon application group definition can be used.

## Converged Marathon Application Group

To deploy the converged Portworx/etcd application group:

* Download the DCOS CLI
* Download the [px\_etcd.json file](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/px_etcd.json?raw=true)
* Run the command : `dcos marathon group add px_etcd.json`

The `px_etcd.json` file assumes that :

* Portworx will use all disks/partitions that are not currently mounted or in use
* The same network interfaces will be used for `mgmt` and `data` traffic

If customization is needed here, then users should update the `args` section for the `portworx` definition to provide more specific [command line arguments](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/install/docker.html#run-px).

