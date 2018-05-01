---
layout: page
title: Run Portworx with Docker UCP
keywords: >-
  portworx, PX-Developer, container, docker ucp, docker universal control plane,
  storage
sidebar: home_sidebar
redirect_from: /run-with-docker-ucp.html
---

# ucp

* TOC

  {:toc}

You can use Portworx to implement storage for Docker Universal Control Plane \(UCP\). This section is qualified using Docker 1.11 and Universal Control Plane 1.1.2.

## Step 1: Install and license Docker UCP

Follow the instructions for [Installing Docker UCP](https://docs.docker.com/ucp/installation/install-production).

> **Note:**  
> You must run Docker Commercially Supported \(CS\) Engine.

After installing Docker UCP, you must [license your installation](https://docs.docker.com/ucp/installation/license).

## Step 2: Update your docker.service file

Not all nodes within a UCP cluster will necessarily be running Portworx. For UCP to properly identify Portworx nodes, the Docker Daemon must start with a Label that indicates Portworx is running. To make that happen, you will need to update the docker.service file.

To find where your docker.service file is located, run the `systemctl` command:

```text
[...]
systemctl status docker
[...]
```

Then, replace the existing `ExecStart` line with the one for your Docker version.

**Docker 1.11**

```text
[...]
ExecStart=/usr/bin/docker daemon -H fd:// --label pxfabric=px-cluster1
[...]
```

**Docker 1.12**

```text
[...]
ExecStart=/usr/bin/dockerd --label pxfabric=px-cluster1
[...]
```

Next, reload `systemctl` and restart the docker daemon:

```text
# systemctl daemon-reload
# systemctl restart docker
```

You can now verify that the label is in place for whichever nodes are running Portworx:

```text
# docker info
[...]
Labels:
    pxfabric=px-cluster1
[...]
```

> **Note:**  
> You can use the fabric label to specify different PX-based clusters.

## Step 3: Launch a container

To launch a container from the command line and restrict it to running on a node that is running Portworx:

```text
docker  run -d -P -e constraint:pxfabric==px-cluster1 --name db mysql
```

Or, from the UCP GUI for launching a container, specify a constraint for `pxfabric` as follows: ![UCP GUI constraints](../../.gitbook/assets/constraints.png){:width="791px" height="148px"}

For more information on Docker Filters and Constraints, see [Swarm filters](https://docs.docker.com/swarm/scheduler/filter/).

