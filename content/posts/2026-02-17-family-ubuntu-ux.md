---
title: "家用 Ubuntu 用户体验"
date: 2026-02-13T09:30:49+08:00
draft: true
description: ""
showHero: false
series: [家用 Ubuntu 系统]
series_order: 4
categories: []
tags: []
slug: ""
---

> 本文讲述如何设置文件夹名称、软件等，减轻父母的上手难度



常见用途设置。

## 无密码登录

因为Ubuntu安装应用与Windows不一样，由我来安装所有软件。而父母要求，完全不想记密码，无论是登录还是正常使用过程中都不要出现密码。因此设计为双账户，我使用带密码的管理员账户，父母使用无密码的普通账户。

Ubuntu图形化界面要求所有用户都有密码，因此要用命令行的方式从底层删除普通用户的密码。

```bash
su admin
sudo passwd -d <family>
```

此时再次查看用户详情，发现提示无密码。

## 文件夹自欺欺人的改名