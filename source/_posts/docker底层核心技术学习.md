---
title: docker底层核心技术学习
categories:
  - 笔记
pageview: 1
date: 2019-06-17 13:53:03
tags:
  - docker
cover: https://qiniublog.whitedolphin.top/5599347-df87d5e741077be8.png
---
**以下总结一些本人自学docker过程中的一些笔记，不保证内容的准确性**

## 1. 云平台的三个层次服务
- IaaS 基础设施即服务
> 通过互联网提供数据中心、基础架构硬件和软件资源，还可以提供服务器、操作系统、磁盘存储、数据库和/或信息资源。
- PaaS 平台即服务
> 提供了基础架构，软件开发者可以在这个基础架构之上建设新的应用，或者扩展已有的应用，同时却不必购买开发、质量控制或生产服务器。
- SaaS 软件即服务
> 一种软件分布模式，在这种模式下，应用软件安装在厂商或者服务供应商那里，用户可以通过某个网络来使用这些软件，通常使用的网络是互联网。
---
## 2. docker解决的问题
> docker是一种虚拟化技术。它的出现解决了后端的开发和运维阶段中开发环境和生产环境一致的问题。通过Docker我们可以将程序运行的环境纳入到版本控制中，排除因为环境的不同运行结果的可能。

**简单说就是以下几点：**
- 高效资源利用
```html
单台极其可部署多个应用
应用之间互相隔离
应用之间不会发生资源抢占
```
- 一次编译，随处运行

## 3.docker的实现原理
``` html
docker底层核心技术从三方面入手隔离容器
1. namespace --- 系统
2. cgroup -- 资源
3. AUFS --文件系统
```

### 3.1Namespaces 命名空间
![linux Namespaces](https://qiniublog.whitedolphin.top/5599347-df87d5e741077be8.png)

>   在Linux系统中，可以同时存在多用户多进程，系统的整体资源是有限的，Namespaces机制提供了一种资源隔离的方案。

> PID,IPC,Network等系统资源不再是全局性的，而是属于某个特定的Namespace。每个namespace下的资源对于其他namespace下的资源都是透明，不可见的。因此在操作系统层面上看，就会出现多个相同pid的进程。系统中可以同时存在两个进程号为0,1,2的进程，由于属于不同的namespace，所以它们之间并不冲突。

![namespace隔离](https://qiniublog.whitedolphin.top/5599347-765ec4bc2c1a4e86.png)

> 简单理解就是每个namespace看上去就像一个单独的linux系统。
从上图也可以看出，存在一个基础的父namespace，子A和子B相互独立隔离开来，父Namespace可以查看子A、子B中的所有进程

- Namespace隔离了什么？
> 目前实现的内容包括以下六种类型

Namespace类型|系统调用参数|内核版本
:-:|:-:|:-:
Mount namespaces|CLONE_NEWNS|2.4.19
UTS namespaces|CLONE_NEWUTS|2.6.19
IPC namespaces|CLONE_NEWIPC|2.6.19
PID namespaces|CLONE_NEWPID|2.6.24
Network namespaces|CLONE_NEWNET|2.6.29
User namespaces|CLONE_NEWUSER|3.8
  
### 3.2 cgroup介绍
 > Docker容器使用namespace来隔离运行环境，这样使容器中的进程看起来像使一个独立环境中运行一样。
但是只有namespace还不够！因为这些进程还是可以不受限制的使用系统资源，这样如果一个进程占用太多资源会影响其它进程，如果系统的资源被耗尽，linux还会触发OOM，这样会杀掉一些进程。因此，为了让容器更加可控，使用cgroup来限制容器中的进程允许使用的系统资源。

- 原理
> linux cgroup可以为系统中所运行任务(进程)的用户定义组群分配资源。

> 比如CPU时间、系统内存、网络宽带等资源的组合。可以监控管理员配置的cgroup，拒绝cgroup访问某些资源，也可以在运行的系统中动态配置cgroup。也就是说它以一组进程为目标进行系统资源分配和控制。

- 提供的功能
>  1. Resource limitation:  限制资源使用，比如内存使用上限以及文件系统缓存限制
    2. Prioritization： 优先级控制，比如：CPU利用和磁盘IO吞吐
    3. Accounting： 一些审计或统计
    4. Controll： 挂起进程，恢复执行进程

> 使用 cgroup，系统管理员可更具体地控制对系统资源的分配、优先顺序、拒绝、管理和监控。可更好地根据任务和用户分配硬件资源，提高总体效率.

### 3.3 UnionFS和AUFS以及overlay2
> UnionFS使一种为Linux，FreeBSD，NetBSD操作系统设计的，它能把其它文件系统联合到一个联合挂载点的文件系统服务。
它使用branch把不同文件系统的文件和目录透明地覆盖，形成一个单一一致的文件系统。

> AUFS完全重写了UnionFS1.X，主要目的使为了可靠性和性能，并且引入了一些新的功能，比如可写分支的负载均衡。

> docker选用的第一种存储驱动是AUFS。具有快速启动容器高效利用存储和内存的优点，直到现在AUFS仍然是Docker支持的一种存储驱动类型。

> 后来docker优选overlay2

## 4. docker daemon
> Docker守护进程(Docker Daemon)。它是运行在操作系统之上的后台进程，负责管理Docker容器。Docker守护进程可以直接与主操作系统进行通信，为各个Docker容器分配资源；它还可以将容器与主操作系统隔离，并将各个容器互相隔离。


**以上都是概念和干货，是为了加深对docker的理解而学习到的**
