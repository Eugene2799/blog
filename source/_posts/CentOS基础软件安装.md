---
title: CentOS基础软件安装
date: 2019-06-15 11:22:18
categories:
- 笔记
tags:
- centos
cover: 
pageview: 110
---

> 先更新一下系统
```bash

$ yum update
$ yum upgrade
$ reboot

```
别忘了[升级内核](https://www.jianshu.com/p/d06781acc1c8)
还有[配置ssh端口及免密码](https://www.jianshu.com/p/9b2b9d29ff03)


> 以下软件非必须安装，但因个人习惯，我常安装的列表如下：
```bash

$ yum -y install vim
$ yum -y install bash-completion
$ yum -y install net-tools
$ yum -y install nmap

```
> 开发环境一键安装：
```bash

$ yum groupinstall "Development Tools"

```
