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
\#\# Interactive Tutorial



Following are some interactive tutorials that give an overview about Portworx on Kubernetes.



\* \[Portworx on Kubernetes\]\(https://www.katacoda.com/portworx/scenarios/deploy-px-k8s\) gives a high level overview on installing Portworx on Kubernetes.

\* \[Persistent volumes on Kubernetes using Portworx\]\(https://www.katacoda.com/portworx/scenarios/px-k8s-vol-basic\) explains how to create persistent volumes using Portworx on Kubernetes.



\#\# Prerequisites



{% include px-prereqs.md %}.



\#\# Install



Portworx gets deployed as a \[Kubernetes DaemonSet\]\(https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/\). Following sections describe how to generate the spec files and apply them.



\#\#\#\# Generating the spec



{% include k8s-spec-generate.md %}



\#\#\#\# Internal Kvdb \(beta\)



Portworx can be configured to run with internal kvdb by enabling it in the above spec generator.
{% endtab %}

{% tab title="GKE Install" %}

{% endtab %}

{% tab title="AKS Install" %}

{% endtab %}

{% tab title="Openshift Install" %}

{% endtab %}
{% endtabs %}

## Join us on Slack!

Join us on [slack](http://slack.portworx.com)

