---
title: "Portainer"
date: 2026-01-31T01:33:21+08:00
draft: true
description: ""
showHero: false
slug: "portainer"
---

## 部署

**安装**

[中文翻译版](https://www.portainer.cn/)

**配置**

**运行**

中文portainer

```sh
docker run -d --name portainer -p 4000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v <portainer_data>:/data --privileged=true 6053537/portainer-ce
```

```sh
docker run -d --name portainer -p 4000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /volume1/docker/portainer:/data --privileged=true 6053537/portainer-ce
```

agnet

```sh
docker run -d -p 5000:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes -v /:/host portainer/agent:2.33.3
```
