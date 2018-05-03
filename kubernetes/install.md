---
layout: page
title: Deploy Portworx on Kubernetes
keywords: >-
  portworx, container, Kubernetes, storage, Docker, k8s, flexvol, pv, persistent
  disk
sidebar: home_sidebar
---

# Install

Portworx can run alongside Kubernetes and provide Persistent Volumes to other applications running on Kubernetes. This section describes how to deploy PX within a Kubernetes cluster and have PX provide highly available volumes to any application deployed via Kubernetes.

## Deploy PX with Kubernetes 1.6+

Kubernetes-1.6 [release](https://github.com/kubernetes/kubernetes/releases/tag/v1.6.0) includes the Portworx native driver support which allows Dynamic Volume Provisioning.

The native portworx driver in Kubernetes supports the following features: 1. Dynamic Volume Provisioning 2. Storage Classes 3. Persistent Volume Claims 4. Persistent Volumes

## Prerequisites

You must have a running Kubernetes 1.6+ Cluster. If your Kubernetes cluster is older than 1.6, follow [these](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/run-with-kubernetes-flexvolume.html) instructions to run Kubernetes with flexvolume.

### Optional

To enable Scheduler Convergence, you need to provide PX with the Kubernetes configuration and certificate files.

A `kubernetes.yaml` file is needed for allowing PX to communicate with Kubernetes. This configuration file primarily consists of the kubernetes cluster information and the kubernetes master node's IP and port where the kube-apiserver is running. This file, and any kubernetes certificates, need to be located at

`/etc/pwx/`

```text
# cat /etc/pwx/kubernetes.yaml
```

```yaml
apiVersion: v1
kind: Config
clusters:
- cluster:
    api-version: v1
    server: http://<master-node-ip>:<api-server-port>
    certificate-authority: /etc/pwx/my_cafile
preferences:
  colors: true
```

> **Note:**  
> The above kubernetes.yaml file is exactly same as the kubelet config file usually named as admin.conf. You need to just copy that file into /etc/pwx/ and rename it to kubernetes.yaml

## Install

> **Note:**  
> If you are deploying Kubernetes using [Tectonic](https://coreos.com/tectonic/), read the [known issue here](install.md#tectonic-known-issue).

The following kubectl command deploys Portworx in the cluster as a `daemon set`:

```text
# kubectl apply -f "http://install.portworx.com?cluster=mycluster&kvdb=etcd://etc.company.net:4001"
```

Make sure you change the custom parameters \(_cluster_ and _kvdb_\) to match your environment.

Below are all parameters that can be given in the query string:

| Key | Description | Example |
| --- | --- | --- |
| cluster | \(Required\) Specifies the unique name for the Portworx cluster. | cluster=test\_cluster |
| kvdb | \(Required\) Points to your key value database, such as an etcd cluster or a consul cluster. | kvdb=etcd://etcd.fake.net:4001 |
| drives | \(Optional\) Specify comma-separated list of drives. | drives=/dev/sdb,/dev/sdc |
| diface | \(Optional\) Specifies the data interface. This is useful if your instances have non-standard network interfaces. | diface=eth1 |
| miface | \(Optional\) Specifies the management interface. This is useful if your instances have non-standard network interfaces. | miface=eth1 |
| zeroStorage | \(Optional\) Instructs PX to run in zero storage mode on kubernetes master. | zeroStorage=true |
| force | \(Optional\) Instructs PX to use any available, unused and unmounted drives or partitions.,PX will never use a drive or partition that is mounted. | force=true |
| etcdPasswd | \(Optional\) Username and password for ETCD authentication in the form user:password | etcdPasswd=username:password |
| etcdCa | \(Optional\) Location of CA file for ETCD authentication. | etcdCa=/path/to/server.ca |
| etcdCert | \(Optional\) Location of certificate for ETCD authentication. | etcdCert=/path/to/server.crt |
| etcdKey | \(Optional\) Location of certificate key for ETCD authentication. | etcdKey=/path/to/server.key |
| acltoken | \(Optional\) ACL token value used for Consul authentication. | acltoken=398073a8-5091-4d9c-871a-bbbeb030d1f6 |
| token | \(Optional\) Portworx lighthouse token for cluster. | token=a980f3a8-5091-4d9c-871a-cbbeb030d1e6 |
| env | \(Optional\) Comma-separated list of environment variables that will be exported to portworx. | env=API\_SERVER=[http://lighthouse-new.portworx.com](http://lighthouse-new.portworx.com) |
| coreos | \(Optional\) Specifies that target nodes are coreos. | coreos=true |

#### Scaling

Portworx is deployed as a `Daemon Set`. Therefore it automatically scales as you grow your Kubernetes cluster. There are no additional requirements to install Portworx on the new nodes in your Kubernetes cluster.

#### Examples

```text
# To specify drives
$ kubectl apply -f "http://install.portworx.com?cluster=mycluster&kvdb=etcd://etcd.fake.net:4001&drives=/dev/sdb,/dev/sdc"

# To run on coreos
$ kubectl apply -f "http://install.portworx.com?cluster=mycluster&kvdb=etcd://etcd.fake.net:4001&coreos=true"

# To run in master in zero storage mode and use a specifc drive for other nodes
$ kubectl apply -f "http://install.portworx.com?cluster=mycluster&kvdb=etcd://etcd.fake.net:4001&zeroStorage=true&drives=/dev/sdb"
```

## Upgrade

To upgrade Portworx, use the same `kubectl apply` command used to install it. This will repull the image used for Portworx \(portworx/px-enterprise:latest\) and perform a rolling upgrade.

You can check the upgrade status with following command.

```text
$ kubectl rollout status ds portworx --namespace kube-system
```

## Uninstall

Following kubectl command uninstalls Portworx from the cluster.

```text
$ kubectl delete -f "http://install.portworx.com?cluster=mycluster&kvdb=etcd://etcd.fake.net:4001"
```

> **Note:**  
> During uninstall, the configuration files \(/etc/pwx/config.json and /etc/pwx/.private.json\) are not deleted. If you delete /etc/pwx/.private.json, Portworx will lose access to data volumes.

## Known issues

**Kubernetes on CoreOS deployed through Tectonic**

[Tectonic](https://coreos.com/tectonic/) is deploying the [Kubernetes controller manager](https://kubernetes.io/docs/admin/kube-controller-manager/) in the docker `none` network. As a result, when the controller manager invokes a call on [http://localhost:9001](http://localhost:9001) to portworx to create a new volume, this results in the connection refused error since controller manager is not in the host network. This issue is observed when using dynamically provisioned Portworx volumes using a StorageClass. If you are using pre-provisioned volumes, you can ignore this issue.

To workaround this, you need to set `hostNetwork: true` in the spec file `modules/bootkube/resources/manifests/kube-controller-manager.yaml` and then run the tectonic installer to deploy kubernetes.

Here is a sample [kube-controller-manager.yaml](https://gist.github.com/harsh-px/106a23b702da5c86ac07d2d08fd44e8d) after the workaround.

This issue will be fixed in upcoming kubernetes release 1.6.5.

