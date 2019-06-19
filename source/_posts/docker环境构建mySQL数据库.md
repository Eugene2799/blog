---
title: docker环境构建mySQL数据库
categories:
  - 笔记
pageview: 1
date: 2019-06-17 13:52:25
tags:
 - docker
cover:
---

构建[docker环境](https://www.jianshu.com/p/1b3ae606db81)后：
### 1. 拉取镜像
```bash

$ docker pull mysql:5.7

```
### 2. 创建映射目录
```bash

$ mkdir /opt/mysql
$ mkdir /opt/mysql/conf
$ mkdir /opt/mysql/logs
$ mkdir /opt/mysql/data

```
### 3. 启动容器
```bash

docker run --name mysql5.7 -p 3306:3306 \
            -v /opt/mysql/data:/var/lib/mysql \
            -v /opt/mysql/logs:/logs \
            -v /opt/mysql/conf:/etc/mysql/conf.d \
            --restart=always \
            -e MYSQL_ROOT_PASSWORD=123456 \
            -d mysql:5.7

```

PS: 步骤1、2可省略😄
