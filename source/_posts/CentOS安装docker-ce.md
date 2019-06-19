---
title: CentOS安装docker-ce
categories:
  - 笔记
  - centos
pageview: 1
date: 2019-06-17 13:44:51
tags:
  - centos
cover: https://qiniublog.whitedolphin.top/5599347-84ca35699a08dfde.png
---

安装根据[docker安装步骤](https://docs.docker.com/install/linux/docker-ce/centos/)操作


### 1. 系统要求
Docker CE安装需要64位版本的CentOS 7.

### 2. 卸载旧版本
如果安装了之前版本的docker，需要先卸载。
```bash

$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

```
如果yum提示没有安装这些安装包，表示情况正常。

### 3. 使用镜像仓库进行安装
- 安装如下软件包：
```bash

$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2

```
![](https://qiniublog.whitedolphin.top/5599347-84ca35699a08dfde.png)

- 使用如下命令设置stable镜像仓库：
```bash

$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

```
![](https://qiniublog.whitedolphin.top/5599347-3610bfe03fef1575.png)
> 如果失败了就多试两次，因为不可描述的原因或是网络不好导致失败很正常。多次不行的话，或者更换国内安装源，比如阿里源：
```bash

$ sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

```
### 4. 安装docker-ce
``` bash

$ sudo yum install docker-ce

```
![安装完成](https://qiniublog.whitedolphin.top/5599347-49b04cf31d7e598d.png)
> 同理，如果安装失败就多执行几次安装命令。

### 5. 开启远程访问API
如果你的CentOS上的docker是作为远程服务的，则在/usr/lib/systemd/system/docker.service中，配置远程访问。
在[Service]中加入-H tcp://0.0.0.0:2375
![](https://qiniublog.whitedolphin.top/5599347-8fe261dbfe67ec1e.png)

### 6. 启动docker
- 配置Docker为启动时启动
```bash

$ systemctl enable docker

```
- 启动docker
``` bash

$ systemctl start docker

```
- 停止docker
```bash

$ systemctl stop docker

```
- 查看docker运行状态
```bash

$ systemctl status docker

```

### 7. 配置非root用户使用docker
> 因为一些原因，我们需要使用非root用户来运行docker，但默认情况下其它用户只有使用*sudo*命令操作才能使用docker。如果不想使用*sudo*命令来操作docker，可以创建一个docker Unix group，将非root用户加入这个group，就可以使用了。[参考文档](https://docs.docker.com/install/linux/linux-postinstall/)

- 创建docker group
``` bash

$ sudo groupadd docker

```
- 将用户加入到docker group
我的用户名叫newbirds，所以是：

```bash

$ sudo usermod -aG docker newbirds

```
- 重新登录
```bash

$ logout

```
![状态图](https://qiniublog.whitedolphin.top/5599347-965dda5a59945a90.png)
> 可以看见之前执行docker ps会提示权限错误，之后执行docker ps就正常了
