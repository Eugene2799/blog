---
title: CentOS修改ssh端口号及ssh免密码登录
date: 2019-06-15 11:29:16
categories:
- 笔记
- centos
tags:
- centos
cover: https://qiniublog.whitedolphin.top/5599347-e9f8213c46666d56.png
pageview: 120
---

### 1. 修改ssh端口号

一般ssh的默认端口为22
比如正常使用终端登录局域网电脑

```bash

ssh root@192.168.0.115

```

#### 1.1 修改sshd配置

注意是 **sshd_config** 文件

```bash

vim /etc/ssh/sshd_config

```

在#Port 22行去掉#号注释，同时加入想要使用的端口号如 Port 22438
![效果图](https://qiniublog.whitedolphin.top/5599347-e9f8213c46666d56.png)
这里保留22端口是为了防止当前无法使用ssh登录

#### 1.2 修改防火墙配置

```bash

firewall-cmd --zone=public --add-port=22438/tcp --permanent

```

成功返回success
重启防火墙：
``` bash

$ firewall-cmd --reload

```
成功返回success
查看添加端口是否成功，成功则显示yes，否则为no：
```bash

$ firewall-cmd --zone=public --query-port=22438/tcp

```
#### 1.3 修改SELinux
查看本机SELinux状态,如果是关闭则可以跳过此步骤
```bash

$ sestatus

```
以下使用semanage操作，没有可以使用以下命令安装：
```bash

$ yum install policycoreutils-python.x86_64

```
以下命令为查看当前SELinux允许的ssh端口：
```bash

$ semanage port -l | grep ssh

```
![查看状态](https://qiniublog.whitedolphin.top/5599347-1d437436a132294c.png)
添加22438端口到SELinux,并确认
```bash

$ semanage port -a -t ssh_port_t -p tcp 22438
$ semanage port -l | grep ssh

```
![](https://qiniublog.whitedolphin.top/5599347-ecd8907affb9700b.png)
#### 1.4 重启ssh服务
```bash

$ systemctl restart sshd.service

```
测试新端口ssh连接
``` bash

$ ssh -p 22438 root@192.168.0.115

```
可以正常登录即成功
#### 1.5 关闭22端口
```bash

$ vim /etc/ssh/sshd_config

```
将之前开放的Port 22一行注释掉并保存
![](https://qiniublog.whitedolphin.top/5599347-9879631a118e489f.png)
然后重启ssh
```bash

$ systemctl restart sshd.service

```
使用以下命令无法登录服务器
```bash

$ ssh root@192.168.0.115

```

### 2. ssh免密码登录

#### 2.1 在本机生成公钥
本机终端输入：
```bash

$ ssh-keygen -t rsa

```
配置相关全部直接回车
在～/.ssh/目录下生成两个文件
```
id_rsa : 私钥
id_rsa.pub : 公钥
```

#### 2.2 导入公钥到认证文件

在本机终端输入：
```bash

$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

```
#### 2.3 导入要免密码登录的服务器
在本机将认证文件复制到服务器
```bash

$ scp -P 22438 ~/.ssh/authorized_keys root@192.168.0.115:/root/.ssh/

```
如果服务器上没有文件夹就新建一个.ssh/文件夹
#### 2.4 在服务器上更改权限
```bash

$ chmod 700 ~/.ssh
$ chmod 600 ~/.ssh/authorized_keys

```
