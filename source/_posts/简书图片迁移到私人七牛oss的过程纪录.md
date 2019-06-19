---
title: 简书图片迁移到私人七牛oss的过程纪录
categories:
  - 笔记
pageview: 1
date: 2019-06-18 11:03:28
tags:
cover: https://qiniublog.whitedolphin.top/20190618112120.png
---

## 起因
本来今天就很闷，昨晚又失眠，感觉自己啥也不会。
找工作就是这样，面试官觉得你是个菜鸡，不过也无所谓了。
然后翻出来这个新搭的blog，发现有些图片看不见了。
一开始以为是手机网络问题，后来反应了一下，心里mmp，肯定是简书的oss服务限制了其它域名的访问。
真是抠门的公司啊，做个软件图片都这么扣。
然后就开始了迁移图片到私人的七牛oss之路。

## 不是个例
网上一搜，别人也遇到过一样的事情。于是就按这哥们的步骤操作了一边。
还真是省事，又学会了些sed命令的操作，于是就纪录一下。

## 操作
因为图片配置都是写的markdown，而且在很多个文件里都有，所以第一步就是要把所有简书的图片链接取出来。
- 1. 取链接
一行行复制？那会死人的。终端一行代码搞定。
```bash
cat ./* | grep upload-images.jianshu.io > image.txt
```
现在所有的简书图片链接都在image.txt文件里面了，格式还是markdown的，这就用vscode里面的批量查找替换功能，轻松搞定。
![最后的模样](https://qiniublog.whitedolphin.top/20190618111348.png)
搞成这种之后，就进行第二步了。
- 2. 下载简书里的原图片
有了image.txt里的内容，第二步也就很简单了。建个文件夹，然后下载图片，三行命令轻松搞定。
```bash
mkdir img
cd img
wget -i ../image.txt
```
- 3. 使用图床软件[picGo](https://molunerfinn.com/PicGo/)上传到七牛oss
软件很简单，但是太方便了，原来大家都是用这种工具上传图片然后些markdown的吗？
发现了新的超级好用的工具。
需要注意点的是，配置七牛图床的时候
![存储区域配置](https://qiniublog.whitedolphin.top/20190618112120.png)
默认是z0，这肯定是区域代码了。z0是哪里咱也不知道，咱也不敢问。不过我的是华南，试了一下是z2.z0可能是华东吧。
将所有图片上传好之后，就是学习sed的地方了。
- 4. 替换新的链接
```bash
sed -i "s/upload-images.jianshu.io\/upload_images\//qiniublog.whitedolphin.top\//" ./*
```
还是之前寸文章的目录，但我这里运行报错了，说是没有.命令，搜了搜发现，原来我用的mac系统的sed命令，跟linux的不一样。
**于是mac系统的朋友注意了，下面是配置mac的sed命令**👇
使用homebrew安装gnu版本的sed，要用普通用户，不要用root
```bash
brew install gnu-sed
```
将配置写入.bashrc文件
```bash
export PATH="/usr/local/opt/gnu-sed/libexec/gnubin:$PATH"
```
别忘了source文件。
然后就正常运行了。
- 5. 删除简书图片后缀
最后就是清理简书图片的后缀问题。
两行命令轻松愉快：
```bash
sed -i "s/\?imageMogr2\/auto-orient\/strip\%7CimageView2\/2\/w\/720//" ./*

sed -i "s/\?imageMogr2\/auto-orient\/strip\%7CimageView2\/2\/w\/1240//" ./*
```

于是心情大好。