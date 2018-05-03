---
layout: page
title: Welcome to Portworx Docs
keywords: 'portworx, px-enterprise, px-developer, containers, storage'
sidebar: home_sidebar
youtubeId: 0zTjOly0vkA
description: >-
  This page describes how to get Portworx up and running on Kubernetes and
  different flavors of Kubernetes
---

# Install Portworx on Kubernetes

## Install on Kubernetes

{% tabs %}
{% tab title="Kubernetes Install" %}
### Interactive Tutorial {#interactive-tutorial}

Following are some interactive tutorials that give an overview about Portworx on Kubernetes.

* [Portworx on Kubernetes](https://www.katacoda.com/portworx/scenarios/deploy-px-k8s) gives a high level overview on installing Portworx on Kubernetes.
* [Persistent volumes on Kubernetes using Portworx](https://www.katacoda.com/portworx/scenarios/px-k8s-vol-basic) explains how to create persistent volumes using Portworx on Kubernetes.

### Prerequisites {#prerequisites}

**Key-value store**

Portworx uses a key-value store for it’s clustering metadata. Please have a clustered key-value database \(etcd or consul\) installed and ready. For etcd installation instructions please refer to [this doc](https://docs.portworx.com/maintain/etcd.html).

**Storage**

At least one of the Portworx nodes should have extra storage available, in a form of unformatted partition or a disk-drive.

Storage devices explicitly given to Portworx will be automatically formatted by PX.

**Shared mounts**

If you are running Docker v1.12, you _must_ configure Docker to allow shared mounts propagation \(see [instructions](https://docs.portworx.com/knowledgebase/shared-mount-propogation.html)\), as otherwise Portworx will fail to start.

**Firewall**

Ensure ports 9001-9015 are open between the nodes that will run Portworx.

**NTP**

Ensure all nodes running PX are time-synchronized, and NTP service is configured and running..

### Install {#install}

Portworx gets deployed as a [Kubernetes DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/). Following sections describe how to generate the spec files and apply them.

**Generating the spec**

To generate the spec file, head on to the below URLs for the PX release you wish to use.

* [1.4 Tech Preview](https://install.portworx.com/1.4/).
* [1.3 Stable](https://install.portworx.com/1.3/).
* [1.2 Stable](https://install.portworx.com/1.2/).

Alternately, you can use curl to generate the spec as described in [Generating Portworx Kubernetes spec using curl](https://docs.portworx.com/scheduler/kubernetes/px-k8s-spec-curl.html).

**Secure ETCD and Certificates**

If using secure etcd provide “https” in the URL and make sure all the certificates are in the _/etc/pwx/_ directory on each host which is bind mounted inside PX container.

**Using Secrets to Provision Certificates**

It is recommended to use Kubernetes Secrets to provide ETCD certificates to Portworx. This way, the certificates will be automatically mounted when new nodes join the cluster.

Copy all your etcd certificates and key in a folder _etcd-secrets/_ to create a Kubernetes secret from it.

```text
# ls etcd-secrets
etcd-ca etcd-cert   etcd-key
```

Use `kubectl` to create the secret named `px-etcd-certs` from the above files:

```text
# kubectl -n kube-system create secret generic px-etcd-certs --from-file=etcd-secrets/
```

Now edit the Portworx spec file to reference the certificates. Given the names of the files are `etcd-ca`, `etcd-cert` and `etcd-key`, modify the _volumeMounts_ and _volumes_ sections as follows:

```text
  volumeMounts:
  - mountPath: /etc/pwx/etcdcerts
    name: etcdcerts
```

```text
  volumes:
  - name: etcdcerts
    secret:
      secretName: px-etcd-certs
      items:
      - key: etcd-ca
        path: pwx-etcd-ca.crt
      - key: etcd-cert
        path: pwx-etcd-cert.crt
      - key: etcd-key
        path: pwx-etcd-key.key
```

Now that the certificates are mounted at `/etc/pwx/etcdcerts`, change the portworx container args to use the correct certificate paths:

```text
  containers:
  - name: portworx
    args:
      ["-c", "test-cluster", "-a", "-f",
      "-ca", "/etc/pwx/etcdcerts/pwx-etcd-ca.crt",
      "-cert", "/etc/pwx/etcdcerts/pwx-etcd-cert.crt",
      "-key", "/etc/pwx/etcdcerts/pwx-etcd-key.key",
      "-x", "kubernetes"]
```

**Installing behind the HTTP proxy**

During the installation Portworx may require access to the Internet, to fetch kernel headers if they are not available locally on the host system. If your cluster runs behind the HTTP proxy, you will need to expose _PX\_HTTP\_PROXY_and/or _PX\_HTTPS\_PROXY_ environment variables to point to your HTTP proxy when starting the DaemonSet.

Use _e=PX\_HTTP\_PROXY=&lt;http-proxy&gt;,PX\_HTTPS\_PROXY=&lt;https-proxy&gt;_ query param when generating the DaemonSet spec.

**Internal Kvdb \(beta\)**

Portworx can be configured to run with internal kvdb by enabling it in the above spec generator.

> **Note:** Internal Kvdb is in beta and available for PX version &gt; 1.4

**Applying the spec**

Once you have generated the spec file, deploy Portworx.

```text
$ kubectl apply -f px-spec.yaml
```

Monitor the portworx pods

```text
kubectl get pods -o wide -n kube-system -l name=portworx
```

Monitor Portworx cluster status

```text
PX_POD=$(kubectl get pods -l name=portworx -n kube-system -o jsonpath='{.items[0].metadata.name}')
kubectl exec $PX_POD -n kube-system -- /opt/pwx/bin/pxctl status
```

If you are still experiencing issues, please refer to [Troubleshooting PX on Kubernetes](https://docs.portworx.com/scheduler/kubernetes/support.html) and [General FAQs](https://docs.portworx.com/knowledgebase/faqs.html).

### Deploy a sample application {#deploy-a-sample-application}

Now that you have Portworx installed, checkout various examples of [applications using Portworx on Kubernetes](https://docs.portworx.com/scheduler/kubernetes/k8s-px-app-samples.html).[Edit this page on Github](https://github.com/portworx/px-docs/blob/gh-pages/scheduler/kubernetes/install.md)  
{% endtab %}

{% tab title="GKE Install" %}


The steps below will help you enable dynamic provisioning of Portworx volumes in your Google Kurbenetes Engine \(GKE\) cluster.

### Prerequisites {#prerequisites}

**Key-value store**

Portworx uses a key-value store for it’s clustering metadata. Please have a clustered key-value database \(etcd or consul\) installed and ready. For etcd installation instructions refer this [doc](https://docs.portworx.com/maintain/etcd.html).

**Shared mounts**

Portworx 1.3 and higher automatically enables shared mounts.

If you are installing Portworx 1.2, you _must_ configure Docker to allow shared mounts propagation \(see [instructions](https://docs.portworx.com/knowledgebase/shared-mount-propogation.html)\), as otherwise Portworx will fail to start.

**Firewall**

Ensure ports 9001-9015 are open between the nodes that will run Portworx. Your nodes should also be able to reach the port KVDB is running on \(for example etcd usually runs on port 2379\).

**NTP**

Ensure all nodes running PX are time-synchronized, and NTP service is configured and running.

**PX Version**

Support for GKE is available only in portworx release version 1.4 and above.

### Create a GKE cluster {#create-a-gke-cluster}

Portworx is supported on GKE cluster provisioned on [Ubuntu Node Images](https://cloud.google.com/kubernetes-engine/docs/node-images). So it is important to specify the node image as **Ubuntu** when creating clusters.

More information about creating GKE clusters can be found [here](https://cloud.google.com/kubernetes-engine/docs/clusters/operations).

### Install {#install}

Portworx gets deployed as a [Kubernetes DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/). Following sections describe how to generate the spec files and apply them.

#### Disk template {#disk-template}

Portworx takes in a disk spec which gets used to provision GCP persistent disks dynamically.

A GCP disk template defines the Google persistent disk properties that Portworx will use as a reference. There are 2 ways you can provide this template to Portworx.

**1. Using a template specification**

The spec follows the following format:

```text
"type=<GCP disk type>,size=<size of disk>"
```

* **type**: Following two types are supported
  * _pd-standard_
  * _pd-ssd_
* **size**: This is the size of the disk in GB

See [GCP disk](https://cloud.google.com/compute/docs/disks/) for more details on above parameters.

Examples:

* `"type=pd-ssd,size=200"`
* `"type=pd-standard,size=200","type=pd-ssd,size=100"`

**2. Using existing GCP disks as templates**

You can also reference an existing GCP disk as a template. On every node where PX is brought up as a storage node, a new GCP disk\(s\) identical to the template will be created.

For example, if you created a template GCP disk called _px-disk-template-1_, you can pass this in to PX as a parameter as a storage device.

Ensure that these disks are created in the same zone as the GCP node group.

#### Generate the spec {#generate-the-spec}

We will supply the template\(s\) explained in previous section, when we create the Portworx spec.

To generate the spec file, head on to the below URLs for the PX release you wish to use.

* [1.4 Tech Preview](https://install.portworx.com/1.4/).

Alternately, you can use curl to generate the spec as described in [Generating Portworx Kubernetes spec using curl](https://docs.portworx.com/scheduler/kubernetes/px-k8s-spec-curl.html).

**Secure ETCD and Certificates**

If using secure etcd provide “https” in the URL and make sure all the certificates are in the _/etc/pwx/_ directory on each host which is bind mounted inside PX container.

**Using Secrets to Provision Certificates**

It is recommended to use Kubernetes Secrets to provide ETCD certificates to Portworx. This way, the certificates will be automatically mounted when new nodes join the cluster.

Copy all your etcd certificates and key in a folder _etcd-secrets/_ to create a Kubernetes secret from it.

```text
# ls etcd-secrets
etcd-ca etcd-cert   etcd-key
```

Use `kubectl` to create the secret named `px-etcd-certs` from the above files:

```text
# kubectl -n kube-system create secret generic px-etcd-certs --from-file=etcd-secrets/
```

Now edit the Portworx spec file to reference the certificates. Given the names of the files are `etcd-ca`, `etcd-cert` and `etcd-key`, modify the _volumeMounts_ and _volumes_ sections as follows:

```text
  volumeMounts:
  - mountPath: /etc/pwx/etcdcerts
    name: etcdcerts
```

```text
  volumes:
  - name: etcdcerts
    secret:
      secretName: px-etcd-certs
      items:
      - key: etcd-ca
        path: pwx-etcd-ca.crt
      - key: etcd-cert
        path: pwx-etcd-cert.crt
      - key: etcd-key
        path: pwx-etcd-key.key
```

Now that the certificates are mounted at `/etc/pwx/etcdcerts`, change the portworx container args to use the correct certificate paths:

```text
  containers:
  - name: portworx
    args:
      ["-c", "test-cluster", "-a", "-f",
      "-ca", "/etc/pwx/etcdcerts/pwx-etcd-ca.crt",
      "-cert", "/etc/pwx/etcdcerts/pwx-etcd-cert.crt",
      "-key", "/etc/pwx/etcdcerts/pwx-etcd-key.key",
      "-x", "kubernetes"]
```

**Installing behind the HTTP proxy**

During the installation Portworx may require access to the Internet, to fetch kernel headers if they are not available locally on the host system. If your cluster runs behind the HTTP proxy, you will need to expose _PX\_HTTP\_PROXY_and/or _PX\_HTTPS\_PROXY_ environment variables to point to your HTTP proxy when starting the DaemonSet.

Use _e=PX\_HTTP\_PROXY=&lt;http-proxy&gt;,PX\_HTTPS\_PROXY=&lt;https-proxy&gt;_ query param when generating the DaemonSet spec.

#### Applying the spec {#applying-the-spec}

Once you have generated the spec file, deploy Portworx.

```text
$ kubectl apply -f px-spec.yaml
```

Monitor the portworx pods

```text
kubectl get pods -o wide -n kube-system -l name=portworx
```

Monitor Portworx cluster status

```text
PX_POD=$(kubectl get pods -l name=portworx -n kube-system -o jsonpath='{.items[0].metadata.name}')
kubectl exec $PX_POD -n kube-system -- /opt/pwx/bin/pxctl status
```

If you are still experiencing issues, please refer to [Troubleshooting PX on Kubernetes](https://docs.portworx.com/scheduler/kubernetes/support.html) and [General FAQs](https://docs.portworx.com/knowledgebase/faqs.html).

### Deploy a sample application {#deploy-a-sample-application}

Now that you have Portworx installed, checkout various examples of [applications using Portworx on Kubernetes](https://docs.portworx.com/scheduler/kubernetes/k8s-px-app-samples.html).

### Troubleshooting Notes {#troubleshooting-notes}

* The `kubectl apply ...` command fails with “forbidden” error:

  * If you encounter an error with the cluster role permission \(`clusterroles.rbac.authorization.k8s.io "portworx-pvc-controller-role" is forbidden`\), create a ClusterRoleBinding for your user using the following commands:

  ```text
   # get current google identity
   $ gcloud info | grep Account
   Account: [myname@example.org]

   # grant cluster-admin to your current identity
   $ kubectl create clusterrolebinding myname-cluster-admin-binding \
      --clusterrole=cluster-admin --user=myname@example.org
   Clusterrolebinding "myname-cluster-admin-binding" created
  ```

* GKE instances under certain scenarios do not automatically re-attach the persistent disks used by PX.
  * Under the following scenarios, GKE will spin up a new VM as a replacement for older VMs with the same node name:
    * Halting a VM in GKE cluster
    * Upgrading GKE between different kubernetes version
    * Increasing the size of the node pool
  * However, in these cases the previously attached persistent disks will not be re-attached automatically.
  * Currently you will have to manually re-attach the persistent disk to the new VM and then restart portworx on that node.
{% endtab %}

{% tab title="AKS Install" %}

{% endtab %}

{% tab title="Openshift Install" %}

{% endtab %}
{% endtabs %}

## Join us on Slack!

Join us on [slack](http://slack.portworx.com)

