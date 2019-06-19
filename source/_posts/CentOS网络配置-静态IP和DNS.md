---
title: CentOS网络配置-静态IP和DNS
date: 2019-06-15 11:28:14
categories:
- 笔记
tags:
- centos
cover: https://qiniublog.whitedolphin.top/eb5e25f41bd5ad6e33882b6a8acb39dbb7fd3c86.jpg
pageview: 120
---

#### 静态IP配置
```bash
vim /etc/sysconfig/network-script/ifcfg-eno1
```
修改**BOOTPROTO=static**和**ONBOOT=yes**
***
#### 设置DNS
```bash
vim /etc/resolv.conf
```
加入两行
```
nameserver 223.5.5.5
nameserver 8.8.8.8
```
保存后
#### 重启网络
```bash
systemctl restart network
```
