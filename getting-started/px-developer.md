---
layout: page
sidebar: home_sidebar
title: Get Started with PX-Developer
keywords: 'portworx, px-developer, container, storage, requirements'
redirect_from: /get-started-px-developer.html
---

# px-developer

* TOC

  {:toc}

## Step 1: Verify requirements

* Linux kernel 3.10 or greater
* Docker 1.10 or greater.
* Configure Docker to use shared mounts.  The shared mounts configuration is required, as PX-Developer exports mount points.
  * Run sudo mount --make-shared / in your SSH window
  * If you are using systemd, remove the `MountFlags=slave` line in your docker.service file.
* A kev/value store such as Etcd 2.0 or Consul 0.7.0
* Minimum resources per server:
  * 4 CPU cores
  * 4 GB RAM
* Recommended resources per server:
  * 12 CPU cores
  * 16 GB RAM
  * 128 GB Storage
  * 10 GB Ethernet NIC
* Maximum nodes per cluster:
  * 20 server nodes
* Open network ports:
  * Ports 9000 - 9005 must be open for internal network traffic between nodes running PX

## Step 2: Install and run PX-Developer

See our quick start guides:

* [Run PX-Developer with Docker](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/install/docker.html)
* [Run PX-Developer with Docker Compose](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/install/docker-compose.html)

Run Portworx with schedulers:

* [Run Portworx with Kubernetes](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/scheduler/kubernetes/install.html)
* [Run Portworx with Mesosphere](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/scheduler/mesosphere-dcos/install.html)
* [Run Portworx with Rancher](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/scheduler/rancher.html)

Run stateful containers with Docker volumes:

* [Application Solutions](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/application-solutions.html)

Use **pxctl** \([CLI Reference](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/control/cli.html)\) to directly:

* View the cluster global capacity and health
* Create, inspect, and delete storage volumes
* Attach policies for IOPs prioritization, maximum volume size, and enable storage replication

If you run into an issue:

* [Troubleshooting](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/knowledgebase/troubleshooting.html)

As you use PX-Developer, please share your feedback and ask questions. Find the team on [Google Groups](https://groups.google.com/forum/#!forum/portworx).

If your requirements extend beyond the scope of PX-Developer, please [contact Portworx](http://portworx.com/contact-us/) for information on PX-Enterprise. You can take a tour of the PX-Enterprise console [here](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/getting-started/px-enterprise.html#step-3-take-a-tour-of-the-px-enterprise-web-console).

