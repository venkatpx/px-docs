---
layout: page
title: About PX 1.x
keywords: 'portworx, px-enterprise, release notes'
sidebar: home_sidebar
redirect_from: /about-1-x.html
---

# portworx-1.x

* TOC

  {:toc}

### Portworx released PX-Enterprise 1.0 on September 15, 2016.

## Summary and features

**"PX" Scale-out fabric for containers**

* PX provides scale-out storage for containers. PX is delivered as a container and turns any server it runs on into a converged storage and compute node.
  * PX aggregates all storage LUNs \(local or external\) and creates tiers of storage on each node.
  * PX clusters storage across different nodes in the cluster.
  * PX provides virtual volumes at container granularity.  These volumes are accessible and usable on any node in the cluster.
  * PX will run with any Docker scheduler.
  * PX understands different drive and volume types, including cloud volumes such as EBS \(provisioned IOPS vs GP vs instance local\).

**"Lighthouse" is the Portworx web console to manage multi-tenancy**

* Lighthouse provides storage management for all of your PX-Enterprise deployments, including on-premises clusters and in public clouds.
* Lighthouse monitors health and capacity and lets you provide container-granular storage.

## Limits

Following are the supported limits as tested and qualified by Portworx.

* 1024 nodes per cluster
* 255 volumes per node
* 96 TB per node
* 255 snapshots per volume
* 3 replicas per volume

## Support matrix

* OS Versions: Linux kernel versions 3.10.x through 4.4.x
* Docker versions: Minimum 1.10
* For minimum hardware recommendations, refer to [Get Started with PX-Enterprise](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/getting-started/px-enterprise.html).

## Schedulers

PX-Enterprise is tested and qualified against the following schedulers:

* Kubernetes \(minimum version 1.2\)
* Mesos, Mesosphere, and DC/OS \(minimum version 1.7\)
* Docker UCP running in swarm mode \(minimum version 1.12\)
* Rancher \(minimum version 1.1.3\)
* Amazon ECS

