---
title: "家用 Ubuntu 存储"
date: 2026-02-12T23:41:20+08:00
draft: true
description: ""
showHero: false
series: [家用 Ubuntu 系统]
series_order: 2
categories: []
tags: []
slug: ""
---

电脑为120GB的SSD和1TB的HDD，将数据存在机械硬盘上可以显著降低SSD的负载，即满足应用需要的高速读写，又满足数据的大量存储需求。

## 获取硬盘信息

需要获取硬盘的文件系统和UUID

查找当前设备有什么硬盘

```sh
lsblk
```

列出指定硬盘分区的UUID和文件系统。记下ntfs和uuid的值

```sh
lsblk -d -fs /dev/<location (example: sda2)>
```



## 预处理挂载位置

创建一个空的文件夹，供硬盘挂载。为了防止硬盘未挂载时往目录内部写入数据，导致挂载后被覆盖，直接禁止所有人读写该目录。挂载后会继承硬盘的权限，可以正常使用。

```sh
# 1. 创建目录
sudo mkdir -p /mnt/hdd
# 2. 设置所有权为root用户
sudo chown root:root /mnt/hdd
# 禁止在里访问内部内容
sudo chmod 000 /mnt/hdd
```

## 自动挂载

```sh
sudo nano /etc/fstab
```

```sh
UUID=<your HD UUID> /mnt/<directory name> <FSTYPE> defaults 0 2
```


## 映射文件夹

迁移原文件夹

```sh
mv /home/<user>/.config/QQ /mnt/hdd/Apps/
mv /home/<user>/.xwechat /mnt/hdd/Apps/
mv /home/<user>/.config/qqmusic /mnt/hdd/Apps/
mv /home/gz/.config/QQ /mnt/hdd/Apps/
mv /home/gz/.xwechat /mnt/hdd/Apps/
mv /home/gz/.config/qqmusic /mnt/hdd/Apps/
```

新建软连接

```sh
ln -s /mnt/hdd/Apps/QQ /home/<user>/.config/QQ
ln -s /mnt/hdd/Apps/WeChat /home/<user>/.xwechat
```

```sh
ln -s /mnt/hdd/home/gz/pub /home/gz/公共
ln -s /mnt/hdd/home/gz/Videos /home/gz/视频
ln -s /mnt/hdd/home/gz/tmpl /home/gz/模板
ln -s /mnt/hdd/home/gz/Pictures /home/gz/图片
ln -s /mnt/hdd/home/gz/Documents /home/gz/文档
ln -s /mnt/hdd/home/gz/Downloads /home/gz/下载
ln -s /mnt/hdd/home/gz/Music /home/gz/音乐
ln -s /mnt/hdd/home/gz/Desktop /home/gz/桌面
```


```sh
mv 下载 公共 图片 文档 桌面 模板 视频 音乐 /mnt/hdd/home/vincent/
```

```sh
ln -s /mnt/hdd/home/vincent/pub /home/vincent/公共
ln -s /mnt/hdd/home/vincent/Videos /home/vincent/视频
ln -s /mnt/hdd/home/vincent/tmpl /home/vincent/模板
ln -s /mnt/hdd/home/vincent/Pictures /home/vincent/图片
ln -s /mnt/hdd/home/vincent/Documents /home/vincent/文档
ln -s /mnt/hdd/home/vincent/Downloads /home/vincent/下载
ln -s /mnt/hdd/home/vincent/Music /home/vincent/音乐
ln -s /mnt/hdd/home/vincent/Desktop /home/vincent/桌面
```

## 参考文献

[How do I setup static mount via /etc/fstab for Linux? - Storj Docs](https://storj.dev/node/faq/linux-static-mount)


