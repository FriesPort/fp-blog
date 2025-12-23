---
title: "迁移docker卷"
date: 2025-12-23T22:51:54+08:00
draft: false
description: ""
showHero: false
slug: "migrate-docker-volume"
---

如果你需要完整文档，可以移步[docker engine volume 官方文档](https://docs.docker.com/engine/storage/volumes/#back-up-restore-or-migrate-data-volumes)

## 定义

为了更有指向性的讲解，我们做出如下定义：

- 需要被迁移的容器和卷为 `ctn_old`，`vol_old`。
- 用于过渡操作的中间容器为`ctn_tmp`。
- 新的容器和卷为`ctn_new`，`vol_new`。
- 用于保存备份压缩包的路径为`backup_path`。
- 卷内需要迁移的目标路径为`target_path`。

## 流程

总体思想：
- 备份
    1. 在另一个有终端的过渡容器`ctn_tmp`内同时挂载需要备份的卷`vol_old`和本地备份文件夹`backup_path`。
    2. 将卷的内容打包压缩后放到本地文件夹。
    3. 删除过渡容器。
- 恢复
    1. 创建一个新的卷。
    2. 在中间容器同时挂载本地备份文件夹和新的卷。
    3. 将备份的压缩包解压到目标目录。
    4. 删除过渡容器。

## 案例

接下来以我迁移Authentik数据库为例。

### 备份

模板如下：

压缩指定目录并保存到备份目录。

```bash
docker run --rm \
    -v <vol_old>:<target_path> \
    -v <backup_path>:/backup \
    ubuntu \
    tar cvf /backup/backup.tar <target_path>
```

`docker run`运行一个容器，`--rm`表示容器运行完即删除，两个`-v`分别挂载卷和本地目录，`ubuntu`使用基础Ubuntu镜像作为过渡容器进行操作，`tar cvf`打包一个tar到指定目录。

下面是我的实际命令。

```bash
docker run --rm \
    -v authentik_database:/var/lib/postgresql/data \
    -v $(pwd):/backup \
    ubuntu \
    tar cvf /backup/backup.tar /var/lib/postgresql/data
```

### 恢复

模板如下：

解压缩命备份的安装包。

```bash
docker run --rm \
    -v <vol_new>:<target_path> \
    -v <backup_path>:/backup \
    ubuntu \
    bash -c "tar xvf /backup/backup.tar"
```

下面是我的实际命令。

```bash
docker run --rm \
    -v authentik_database:/var/lib/postgresql/data \
    -v $(pwd):/backup \
    ubuntu \
    bash -c "cd /var/lib/postgresql/data && tar xvf /backup/backup.tar --strip 4"
```

这个命令修改自官方文档，没有调整得太仔细，`--strip 4`代表去除4层外层目录。如果你不喜欢太复杂也可以按照上面的模板填充你的命令。