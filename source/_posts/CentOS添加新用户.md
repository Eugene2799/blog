---
title: CentOS添加新用户
categories:
  - 笔记
  - centos
pageview: 1
date: 2019-06-17 13:42:13
tags:
  - centos
cover: https://qiniublog.whitedolphin.top/5599347-bab00c38918ec530.png
---

### 1. 创建新用户newbirds
```bash

$ adduser newbirds

```
### 2. 设置密码
```bash

$ passwd newbirds

```
### 3. 为新用户授root权限
```bash

查找权限配置文件
$ whereis sudoers
查看权限文件的状态
$ ls -l /etc/sudoers

```
会发现为只读状态
需要修改文件状态为可读可写

```bash

$ chmod -v u+w /etc/sudoers

```
然后修改文件：
```bash

$ vim /etc/sudoers

```
在root ALL=(ALL) ALL下加入新的用户newbirds
![](https://qiniublog.whitedolphin.top/5599347-bab00c38918ec530.png)
保存后，解除文件写权限
```bash

$ chmod -v u-w /etc/sudoers

```