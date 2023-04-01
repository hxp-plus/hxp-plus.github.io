---
layout: post
title: "Enable BBR congestion control on CentOS 8"
author: "Xiping Hu"
header-style: text
tags:
  - Linux
  - Network
---

Add these 2 lines to `/etc/sysctl.conf`:
```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```
apply changes by:
```bash
sysctl -p
```
verify:
```bash
sysctl net.ipv4.tcp_available_congestion_control | grep bbr
```
```
net.ipv4.tcp_available_congestion_control = reno cubic bbr
```
```
lsmod | grep bbr
```
```
tcp_bbr                20480  2
```