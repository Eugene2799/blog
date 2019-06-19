---
title: Navicat12.0.27mac版破解激活(windows可自行查看激活)
categories:
  - 笔记
pageview: 1
date: 2019-06-17 13:46:51
tags:
  - macTools
cover:
---

### 1.下载Navicat
到官网下载[Navicat Premium](https://www.navicat.com.cn/products) 点$\color{rgb(255,0,5,.4)}{免费试用}$ 下载
### 2. 安装
正常安装软件
### 3. 破解激活
是用的激活方式为github的一个开源项目[navicat-keygen](https://github.com/DoubleLabyrinth/navicat-keygen),项目有windows系统激活，也有mac系统激活
![](https://qiniublog.whitedolphin.top/5599347-62067488d60c3502.png)
点击branch切换分支。
下方也有[中文版README](https://github.com/DoubleLabyrinth/navicat-keygen/blob/mac/README.zh-CN.md)可以查看
![](https://qiniublog.whitedolphin.top/5599347-bedb5b48eb774363.png)
#### 3.1破解准备
mac上需要安装brew，git以及四个库openssl, capstone,keystone,rapidjson
打开电脑的终端，输入以下命令，回车搞定。
![](https://qiniublog.whitedolphin.top/5599347-60e0b97bc2c0159a.png)

- [brew安装](https://brew.sh/)
```bash

/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"


```
- git安装
```bash
$ brew install git
```
- 四个库安装
```bash
$ brew install openssl
$ brew install capstone
$ brew install keystone
$ brew install rapidjson
```
#### 3.2拉取代码并编译
- 拉取mac分支代码，并编译keygen和patcher
```bash
$ git clone -b mac https://github.com/DoubleLabyrinth/navicat-keygen.git
$ cd navicat-keygen
$ make all
```
编译完成后，你会在bin/文件夹下看到两个可执行文件：
```bash
$ ls bin/
navicat-keygen    navicat-patcher
```
#### 3.3备份（新安装可略过）
备份好 Navicat Premium.app/Contents/MacOS/Navicat Premium 以及Navicat中所有已保存的数据库连接（包括密码）。
移除所有Navicat在 Keychain.app （即钥匙链）中保存的连接，如果有的话。
你可以通过搜索关键词 navicat 来找到它们。
#### 3.4使用navicat-patcher替换掉公钥
命令navicat-patcher 后是你本地navicat的安装路径
```bash
$ ./navicat-patcher /Applications/Navicat\ Premium.app/Contents/MacOS/Navicat\ Premium
```
完成后会生成一个类似RegPrivateKey.pem的文件
#### 3.4生成一份自签名的代码证书
```bash
1. 打开钥匙串访问
2. 选择创建证书
3. 输入名称“navicat”，身份类型：自签名根证书，证书类型：代码签名
4. 创建
```
![程序钥匙串](https://qiniublog.whitedolphin.top/5599347-6e9f990c97a1eea6.png)
![创建证书](https://qiniublog.whitedolphin.top/5599347-cddef6e7dd4c203e.png)
![创建证书](https://qiniublog.whitedolphin.top/5599347-d53d03938d3022e0.png)
#### 3.5用codesign对Navicat Premium.app重签名
在之前的bin/目录下执行
```bash
$ codesign -f -s "navicat" /Applications/Navicat\ Premium.app/
```
#### 3.6 使用navicat-keygen来生成 序列号 和 激活码
##### 1. 运行以下命令
```bash
./navicat-keygen RegPrivateKey.pem
```
接下来会要求在终端中输入navicat的语言版本，获取一个序列号，还会要求你输入用户名和组织名。
填写之后会要求填写一个请求码，因为我们目前还没有请求码，所以到这一步**不要关闭终端**，也不要输入任何内容。
![运行界面](https://qiniublog.whitedolphin.top/5599347-01cdac9e54189d78.png)
##### 2. 断网，获取请求码
断网后打开Navicat Premium，点击注册按钮，在弹出框中填入上一步keygen生成的 序列号。然后点击激活按钮。
##### 3. 手动激活
激活提示失败后，选择手动激活
##### 4. 获取请求码
在手动激活界面会得到一个请求码，将其复制粘贴到之前终端的keygen里面。按两下回车结束输入。
#### 3.7 激活
将生成的Base64编码的激活码，复制粘贴到手动激活的窗口，点击激活。
