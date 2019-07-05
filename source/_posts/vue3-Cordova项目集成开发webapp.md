---
title: vue3+Cordova项目集成开发webapp
categories:
  - 笔记
pageview: 1
date: 2019-07-05 16:32:23
tags:
  - vue
cover: https://qiniublog.whitedolphin.top/20190705163414.png
---
# 啰嗦介绍
之前使用的vue2+cordova做一些app的小项目，但是vue cli3出来好几个月了，也想着升个级吧，于是搞了搞。思路和之前vue2的思路是一样的。
# 本地环境
```bash
vue -V
3.9.1
cordova --version
9.0.0 (cordova-lib@9.0.1)
```
#具体步骤
首先正确安装vue cli3
```bash
npm install -g @vue/cli
yarn add @vue/cli global
# 查看版本
vue -V
```
而且前提是你的cordova开发环境都配置好了。
```bash
#cordova安装
npm install -g cordova
```
比如android开发的话，需要安装JDK，android studio，SDK，gradle什么的
具体缺什么，可以先创建好项目，然后命令行查看，有提示
```bash
# 新建个项目
cordova create myApp org.apache.cordova.myApp myApp
cd myApp
# 添加个插件
cordova plugin add cordova-plugin-camera
# 添加个android环境
cordova platform add android
# 检查依赖
cordova requirements android
```
创建好cordova项目，配置好依赖后，在项目中再创建vue项目
```bash
vue create vuedemo
```
接下来主要配置vue项目中的内容，使vue build后的文件存储到cordova的www目录下即可进行正常的cordova开发app了
**注意router最好使用hash模式，没试过history模式**
在vuedemo目录下新建vue.config.js文件
配置以下内容：
```javascript
const path = require('path');

module.exports = {
  publicPath: './',
  outputDir: '../www',
  lintOnSave: false,
  chainWebpack: (config) => {
  },
  configureWebpack: (config) => {
    if (process.env.NODE_ENV === 'production') {
      config.mode = 'production'
    } else {
      config.mode = 'development'
    }
    Object.assign(config, {
      resolve: {
        alias: {
          '@': path.resolve(__dirname, './src')
        }
      }
    })
  },
  productionSourceMap: false,
  css: {
    extract: true,
    sourceMap: false,
    loaderOptions: {
      css: {},
      postcss: {}
    },
    modules: false
  },
  parallel: require('os').cpus().length > 1,
  pwa: {},
  devServer: {
    open: process.platform === 'darwin',
    host: '0.0.0.0',
    port: 8088,
    https: false,
    overlay: {
      warnings: true,
      errors: true
    },
    proxy: {
      '/backendproxy': {
        target: 'http://localhost:1234/api',
        changeOrigin: true,
        pathRewrite: {
          '^/backendproxy': ''
        }
      }
    }
  },
  pluginOptions: {}
}
```
这里的配置就可以让vue的项目使用npm run build后生成的文件目录指定到../www/下。
vue cli3项目中，你会发现缺少了之前写调用cordova plugin的static目录，没有关系，static目录虽然没有了，但我们有了public目录，效果是一样的。
