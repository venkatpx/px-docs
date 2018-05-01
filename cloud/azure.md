---
layout: page
title: Portworx on Azure
keywords: 'portworx, azure, PaaS, IaaS, docker, converged, cloud'
sidebar: home_sidebar
redirect_from: /portworx-on-azure.html
---

# azure

* TOC

  {:toc}

This guide shows you how you can easily deploy Portworx on [**Azure**](https://azure.microsoft.com/en-us/)

## Step 1: Provision Virtual Machine

When chosing an instance, verify that you meet the [minimum requirements](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/getting-started/px-enterprise.html#step-1-verify-requirements)

Portworx recommends a minimum cluster size of 3 nodes.

## Step 2: Attach Data Disk to Azure VM

Follow the instuctions from Azure documentation [How to attach a data disk to a Linux VM in the Azure portal ](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-attach-disk-portal/)

Your deployment will look something like following:

![Add Disk](../.gitbook/assets/azure-add-disk.png){:width="1483px" height="477px"}

## Step 3: Install Docker for the appropriate OS Version

Portworx recommends Docker 1.12 with [Device Mapper](https://docs.docker.com/engine/userguide/storagedriver/device-mapper-driver/#/configure-docker-with-devicemapper).

Note: Portworx requires Docker to allow shared mounts. This is standard as of Docker 1.12.  
If you are running Docker without shared mounts, please follow the steps listed [here](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/knowledgebase/shared-mount-propogation.html)

## Step 4: Launch PX-Enterprise

[Follow the instructions to launch PX-Enterprise](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/getting-started/px-enterprise.html)

Use the docker run command to launch PX-Enterprise, substituting the appropriate multipath devices and network interfaces, as identified from the previous steps.

Alternatively, you can either run the 'px\_bootstrap' script from curl, or construct your own [config.json](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/control/config-json.html) file.

From the server node running px-enterprise container, you should see the following status:

![PX-Cluster on Azure](../.gitbook/assets/azure-pxctl-status.png){:width="807px" height="443px"}

You should also be able to monitor cluster from PX-Enterprise console:

![Azure-Cluster on Lighthouse](../.gitbook/assets/azure-cluster-on-lighthouse-updated.png){:width="1404px" height="601px"}

