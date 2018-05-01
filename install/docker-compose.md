---
layout: page
title: Run PX-Developer with Docker Compose
keywords: 'portworx, PX-Developer, container, Docker Compose, storage'
sidebar: home_sidebar
redirect_from: /run-with-compose.html
---

# docker-compose

You can run PX-Developer with [docker-compose](https://docs.docker.com/compose/install/) to create a storage cluster for containers, as follows:

```text
# git clone https://github.com/portworx/px-dev.git
# cd px-dev/quick-start
# docker-compose run portworx -daemon -k etcd://myetc.company.com:4001 -c MY_CLUSTER_ID -s /dev/nbd1 -s /dev/nbd2
```

OR, if you have a custom [px configuration file](https://github.com/portworx/px-dev/edit/master/quick-start/config.json) at `/etc/pwx/config.json`, you can start PX-Developer as follows:

```text
# docker-compose up -d
```

You now have a scale-out storage cluster for containers. To continue, refer to the resources listed in [Get Started with PX-Developer](https://github.com/venkatpx/px-docs/tree/3f39ba94d6d6d91385dcd6792eb6da61d0016b4d/getting-started/px-developer.html).

