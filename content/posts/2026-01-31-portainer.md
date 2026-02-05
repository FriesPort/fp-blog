---
title: "Portainer 容器管理方案"
date: 2026-01-31T01:33:21+08:00
draft: false
description: ""
showHero: false
tags: 
  - 容器
  - 解决方案
slug: "portainer"
---

## docker 容器部署

### 面板

我选择的是有QQ群6053537汉化组提供的[portainer中文翻译版](https://www.portainer.cn/)，如果需要英文原版可以将镜像修改为`portainer/portainer-ce`，完整配置见[portainer官方文档](https://docs.portainer.io/start/install-ce/server/docker/linux#docker-run)。

```sh
docker run -d \
    --name portainer \
    -p <dash_port>:9000 \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v <portainer_data>:/data \
    --privileged=true \
    6053537/portainer-ce
```

根据薯条港端口规划表，我的部署命令如下：

```sh
docker run -d --name portainer -p 4000:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /mnt/nfs/portainer:/data --privileged=true 6053537/portainer-ce
```

### Agent

同时，可以使用agnet来管理其他服务器上的docker环境。疑似没有认证，建议只暴露于信任网络中。

```sh
docker run -d \
    -p <agent_port>:9001 \
    --name portainer_agent \
    --restart=always \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v /var/lib/docker/volumes:/var/lib/docker/volumes \
    -v /:/host \
    portainer/agent:2.33.3
```

根据薯条港端口规划表，我的部署命令如下：

```sh
docker run -d -p 5000:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes -v /:/host portainer/agent:2.33.3
```

在面板上添加一个docker环境即可。