---
title: "迁移至mihomo"
date: 2026-02-13T18:12:24+08:00
draft: false
description: ""
showHero: false
series: [设置storj node]
series_order: 2
categories: []
tags:
  - storj
slug: "migrate-to-mihomo"
---

查看架构，下载mihomo，如果选择二进制包要自行处理systemd的守护进程，见[官方文档](https://wiki.metacubex.one/startup/service/)。

下载mihomo deb安装包。

使用apt包管理器安装

```bash
sudo apt install ./mihomo-linux-arm64-vx.xx.xx.deb
```

或者使用dpkg包管理器安装

```bash
sudo dpkg -i mihomo-linux-arm64-vx.xx.xx.deb
```

在`/etc/mihomo`路径下创建`config.yaml`，写入下面的配置。所有用`<尖括号>`包裹的都需要自行修改。

```yaml
proxy-providers:
  <provider-name>:
    url: "<机场给你的url>"
    type: http
    interval: 86400
    health-check: {enable: true,url: "https://www.gstatic.com/generate_204", interval: 300}
    override:
      additional-prefix: "[<前缀>]"
proxies:
  - name: "direct"
    type: direct
    udp: true
mixed-port: 7890
ipv6: true
allow-lan: true
unified-delay: false
tcp-concurrent: true
external-controller: 0.0.0.0:9090
external-ui: ui
external-ui-url: "https://github.com/MetaCubeX/metacubexd/archive/refs/heads/gh-pages.zip"
secret: "<你设置的密码，用于控制面板登录>"
geodata-mode: true
geox-url:
  geoip: "https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/geoip-lite.dat"
  geosite: "https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/geosite.dat"
  mmdb: "https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/country-lite.mmdb"
  asn: "https://github.com/MetaCubeX/meta-rules-dat/releases/download/latest/GeoLite2-ASN.mmdb"
find-process-mode: strict
global-client-fingerprint: chrome
profile:
  store-selected: true
  store-fake-ip: true
sniffer:
  enable: true
  sniff:
    HTTP:
      ports: [80, 8080-8880]
      override-destination: true
    TLS:
      ports: [443, 8443]
    QUIC:
      ports: [443, 8443]
  skip-domain:
tun:
  enable: true
  stack: mixed
  device: Meta
  dns-hijack:
    - "any:53"
  auto-route: true
  auto-redirect: true
  auto-detect-interface: true
dns:
  enable: true
  ipv6: true
  enhanced-mode: fake-ip
  fake-ip-filter:
    - "+.storj.io"
    - "*"
    - "+.lan"
    - "+.local"
  default-nameserver:
    - tls://223.5.5.5
    - tls://223.6.6.6
  nameserver:
    - https://doh.pub/dns-query
    - https://dns.alidns.com/dns-query
proxy-groups:
  - name: storj
    type: select
    include-all: true
    exclude-type: direct
rules:
  - PROCESS-NAME,frpc,DIRECT
  - DOMAIN-KEYWORD,storj,storj
  - DOMAIN-SUFFIX,storj.io,storj
  - DST-PORT,7777,storj
```

这个配置主要的功能是允许frpc直连，并将发往storj的流量代理。

设置mihomo开机自启

```bash
sudo systemctl enable mihomo.service
```

启动mihomo服务

```bash
sudo systemctl start mihomo.service
```

停止mihomo服务

```bash
sudo systemctl stop mihomo.service
```

查看mihomo服务状态

```bash
sudo systemctl status mihomo.service
```

关闭mihomo开机自启

```bash
sudo systemctl disable mihomo.service
```
