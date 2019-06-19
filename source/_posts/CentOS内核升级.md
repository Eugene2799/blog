---
title: CentOS内核升级
date: 2019-06-15 11:25:54
categories:
- 笔记
- centos
tags:
- centos
cover: https://qiniu.miiiku.xyz/attach/2018/07/psb3.jpeg
pageview: 120
---
更新方法基于[ELRepo](http://elrepo.org/tiki/tiki-index.php)网站

#### 1. 检查内核版本
```bash

$ uname -sr

```

  ![检查内核版本](https://qiniublog.whitedolphin.top/5599347-fb96fc75c27c9b48.png)

#### 2. 更新内核
``` bash

$ rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

$ rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm

```
![执行后效果图](https://qiniublog.whitedolphin.top/5599347-0e49923cb06bae2b.png)
``` bash

$ yum --disablerepo="*" --enablerepo="elrepo-kernel" list available

```
![执行后效果图](https://qiniublog.whitedolphin.top/5599347-0c70592d1510d5a5.png)

#### 3.安装内核
```bash

$ yum --enablerepo=elrepo-kernel install kernel-ml

```
![执行后效果图](https://qiniublog.whitedolphin.top/5599347-fcea0c63b4687335.png)

#### 4. 设置默认启动项
```bash

$ vim /etc/default/grub

```
令 *GRUB_DEFAULT=0*
![执行后效果图](https://qiniublog.whitedolphin.top/5599347-8de51fd3c1130587.png)
#### 5. 生成grub配置文件
```bash

$ grub2-mkconfig -o /boot/grub2/grub.cfg

```
![生成grub配置文件](https://qiniublog.whitedolphin.top/5599347-918d3ed743319a29.png)

#### 6. 重启并查看内核版本
```bash

$ reboot

```
查看内核版本：
```bash

$ uname -sr

```
![验证内核版本](https://qiniublog.whitedolphin.top/5599347-4c2d777f977d1b7e.png)
