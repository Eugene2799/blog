---
title: vue3+Cordova遇到的坑及填埋
categories:
  - 笔记
pageview: 1
date: 2019-07-09 15:09:02
tags:
cover:
---

## 坑1. api接口不通，报错net::ERR_CLEARTEXT_NOT_PERMITTED

解决：
> 修改config.xml中的两段内容

```html
<widget id="you-app-id" version="1.2.3"
xmlns="http://www.w3.org/ns/widgets" 
xmlns:android="http://schemas.android.com/apk/res/android"
xmlns:cdv="http://cordova.apache.org/ns/1.0">
```

```html
<platform name="android">
  <edit-config file="app/src/main/AndroidManifest.xml" mode="merge" target="/manifest/application">
      <application android:usesCleartextTraffic="true" />
  </edit-config>
</platform>
```

## 坑2. import何时加{}和不加

解释：
  使用export抛出的变量，需要用{}引入
  使用export default抛出的变量，可以直接引入
  一个文件里只能有一个export default，而export可以有多个
  