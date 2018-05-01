---
layout: page
title: Troubleshooting
keywords: 'portworx, troubleshooting, logs, issue'
sidebar: home_sidebar
redirect_from: /troubleshooting.html
---

# troubleshooting

* TOC

  {:toc}

For troubleshooting help, get logs for PX-Enterprise or PX-Developer, just as you do for any other Docker container. For example:

* `docker ps` and get the CONTAINER ID for PX-Developer
* `docker logs [CONTAINER_ID]`

## docker: Error response from daemon: Cannot start container XXX: Path /opt/pwx/bin is mounted on / but it is not a shared mount.

If you get this error, the most likely reason is that you are using systemd to start Docker and and have MountFlags set to slave \(this is the default configuration\).

Use "systemctl status docker" to find the location of the docker.service file. Unset or comment out "MountFlags=slave". Perform "systemctl daemon-reload && systemctl restart docker"

## Failed initial deployment

Upon the **initial** attempt to bring up a Portworx cluster, if the first attempt fails due to misconfiguration, then subsequent attempts may also fail, due to reusing incomplete/incorrect configuration information. Configuration for a given host is always stored in the **/etc/pwx** directory. If retrying a previously failed attempt to configure a node, then the **/etc/pwx** directory needs to be removed.

> **Very Important:**  
>  **Never** change or remove **/etc/pwx** _except_ when configuring a host for the **first** time.

## "No such file or directory" message when running on SELinux

If you have `SELinux` enabled, you may get the following error message:

```text
 # docker run --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw  --volume-driver=pxd -v sql_vol:/var/lib/mysql -d mysql
 docker: Error response from daemon: no such file or directory.
 See 'docker run --help'.
```

To resolve the issue:

```text
 # docker run  --security-opt=label:disable  --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw  --volume-driver=pxd -v  sql_vol:/var/lib/mysql -d mysql
```

You will not need to workaround this after [20834](https://github.com/docker/docker/pull/20834) is merged.

## "permission denied" message in `pxctl`

```text
 pxctl cluster list
 show cluster: Get http://unix.sock/v1/cluster/enumerate: dial unix /var/lib/osd/cluster/osd.sock: connect: permission denied
```

To resolve the issue:

Run as `root` to use the `pxctl` tools. To enable root, run `sudo su`.

## "invalid value... bad mode specified" message when running PX-Developer

The following error occurs when your Docker version is not 1.10 or greater. For example, running with v1.9.1 will report this error:

```text
 invalid value "/var/lib/osd:/var/lib/osd:shared" for flag -v: bad mode specified: shared
```

## "Not enough free space in /dev/shm" message

```text
"Invalid PX configuration: Configuration check failed: Not enough free space in /dev/shm, needs 258MB, available 224MB"
```

To resolve the issue:

```text
mount -o remount,size=1GB /dev/shm
```

## Duplicate NodeID

PX Cluster tries to start, but all nodes are reporting the same NodeID. You probably tried to bring up multiple nodes from a cloned image that already had PX running. The NodeID is generated on the fly as new nodes join the cluster and is stored in the /etc/pwx directory. The act of removing the /etc/pwx directory will cause a new ID to be generated. The config.json tells us what cluster to join and the storage devices to use. For the proper way to clone system images with Portworx via **systemd**, please see [this](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/systemd.html)

