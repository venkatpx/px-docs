---
layout: page
title: OS Configurations for Shared Mounts
keywords: 'portworx, px-developer, shared mounts'
sidebar: home_sidebar
redirect_from: /os-config-shared-mounts.html
---

# shared-mount-propogation

* TOC

  {:toc}

Portworx requires Docker to allow shared mounts.

> **Important:**  
> This section is only for Docker versions earlier than 1.12.

The following sections describe how to configure Docker for shared mounts on [CoreOS](shared-mount-propogation.md#coreos-configuration-and-shared-mounts), [RedHat/CentOS](shared-mount-propogation.md#centos-configuration-and-shared-mounts), and [Ubuntu](shared-mount-propogation.md#ubuntu-configuration-and-shared-mounts). The configuration is required because the Portworx solution exports mount points. The examples use AWS EC2 for servers in the cluster.

## CoreOS Configuration and Shared Mounts

1. Verify that your Docker version is 1.10 or later:

   ```text
   docker -v
   ```

2. Copy the docker.service file for editing:

   ```text
    sudo cp /usr/lib64/systemd/system/docker.service /etc/systemd/system
   ```

3. Edit the docker service file for `systemd`:

   ```text
    sudo vi /etc/systemd/system/docker.service
   ```

4. Remove the `MountFlags` line.
5. Reload the daemon:

   ```text
      sudo systemctl daemon-reload
   ```

6. Restart Docker:

   ```text
      sudo systemctl restart docker
   ```

## RedHat/CentOS Configuration and Shared Mounts

1. Follow the Docker installation guide, [Red Hat Enterprise Linux](https://docs.docker.com/engine/installation/linux/rhel/) and then start the Docker service.
2. Verify that your Docker version is 1.10 or later:

   ```text
    docker -v
   ```

3. Edit the service file for `systemd`:

   ```text
    sudo vi /lib/systemd/system/docker.service
   ```

4. Remove the `MountFlags` line.
5. Reload the daemon:

   ```text
     sudo systemctl daemon-reload
   ```

6. Restart Docker:

   ```text
     sudo systemctl restart docker
   ```

## Ubuntu Configuration and Shared Mounts

1. SSH into your first server.
2. While following the Docker installation guide, [Unbuntu](https://docs.docker.com/engine/installation/linux/ubuntulinux/):
   * Update your apt sources for Ubuntu Trusty 14.04 \(LTS\).
   * Make sure you use Trusty 14.04 as the deb entry in step 7 when you add an entry for Ubuntu.
   * Install Docker and start the Docker service.
3. Verify that your Docker version is 1.10 or later. In your SSH window, run:

   ```text
    docker -v
   ```

4. In your SSH window, run:

   ```text
    sudo mount --make-shared /
   ```

5. If you are using `systemd`, remove the `MountFlags=slave` line in your docker.service file.

   The Ubuntu in this example is not using `systemd`.

## Amazon Linux Configuration and Shared Mounts

NOTE that Amazon Linux EC2 images do not have the [`systemd(1)`](http://man7.org/linux/man-pages/man1/systemd.1.html) service, so the installation instructions are somewhat different:

1. Using [Amazon EC2](https://aws.amazon.com/ec2/), start your Amazon Linux instance \(`Amazon Linux AMI 2016.09.1, ami-f173cc91`\), and SSH into it.
2. Install docker package:

   ```text
   # sudo yum install docker
   ```

3. Verify that your Docker version is 1.10 or later. In your SSH window, run:

   ```text
   # docker -v
   ```

4. In your SSH window, run:

   ```text
   # sudo mount --make-shared /
   ```

5. Add the `--propagation shared` flags to the docker startup script:

   ```text
   # sudo sed -i.bak -e \
    's:^\(\ \+\)"$unshare" -m -- nohup:\1"$unshare" -m --propagation shared -- nohup:' \
    /etc/init.d/docker
   ```

6. Restart docker service:

   ```text
   # sudo service docker restart
   ```

## Verify that shared mounts work

Run the following command to verify that shared mounts are configured and running properly on your system:

```text
# docker run -it -v /mnt:/mnt:shared busybox sh -c /bin/date
```

If shared mounts are not working correctly, then the following error will be thrown

```text
docker: Error response from daemon: linux mounts: Path /mnt is mounted on / but it is not a shared mount.
```

