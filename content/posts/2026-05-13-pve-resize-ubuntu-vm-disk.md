---
title: "如何在 PVE 中调整 Ubuntu 虚拟机的硬盘大小"
date: 2026-05-13T10:30:59+08:00
draft: true
description: ""
showHero: true
series: []
series_order: 0
categories: [软件工程]
tags:
  - PVE
  - Ubuntu
slug: "pve-resize-ubuntu-vm-disk"
---

本文适合 PVE 虚拟机的扩容。

主要分为两个步骤，在 PVE Web UI 处理硬盘相关的设置。然后在服务器中处理软件相关的配置。

## 硬件

选择目标虚拟机-硬件-目标硬盘-磁盘操作-调整大小-增量大小(GiB)

输入要增加的大小，并保存。

如果担心会损坏系统，可以给系统打一个快照。

## 软件

进入 Ubuntu 终端，查看分区情况：

```bash
lsblk
```

正常情况下，磁盘的总大小已经改变，但是具体分区未改变。我的结果如下：

```
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                         8:0    0   192G  0 disk 
├─sda1                      8:1    0     1G  0 part /boot/efi
├─sda2                      8:2    0     2G  0 part /boot
└─sda3                      8:3    0 124.9G  0 part 
  └─ubuntu--vg-ubuntu--lv 252:0    0 124.9G  0 lvm  /
sr0                        11:0    1   2.1G  0 rom
```

接下来需要扩大分区和文件系统。

我需要修改的分区为`sda3`，即 sda 的第3个分区。

```bash
sudo growpart /dev/sda 3
```

我使用的是LVM，因此需要修改LVM的物理卷和逻辑卷。

刷新物理卷 (PV)：

```bash
sudo pvresize /dev/sda3
```

扩展逻辑卷 (LV)，将逻辑卷扩展至 100% 的剩余空间：

```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

扩展文件系统：

```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

现代 Linux 内核支持在线扩容，以上操作大部分情况下无需重启虚拟机即可生效。