---
title: vue与vantUI的使用总结
categories:
  - 笔记
pageview: 1
date: 2019-07-04 23:17:43
tags:
  - vue
cover: https://qiniublog.whitedolphin.top/20190705150309.png
---
###简介
VantUI还是挺好用的，是有赞团队维护的一个适用于Vue框架的移动端UI组件库。
为啥要选用VantUI？因为查了几个组件库vux，element的移动端UI库都不支持vue-cli3，我也就没试，刚巧查到这个vantUI还在更新，所以就用了。

###安装模式
vantUI有三四种安装模式，其中两种我比较推荐。
不管哪种方式，首先都要在项目里添加组件
```bash
npm i vant -S
```

> 1.全局安装

这种安装模式，省时省力，可以说是懒人必备了。
缺点也很明显，所有东西都一次性加进来了，用了的，没用过的。所以包很大，昂余很多。
在main.js下添加如下内容
```javascript
import Vant from 'vant';
import 'vant/lib/index.css';

Vue.use(Vant);
```

> 2.自动按需引入组件

这种安装方式也是官方推荐的方式，稍微麻烦点的是每次文件第一次使用都要引用一下。
首先要安装一个插件叫babel-plugin-import，是基于babel的插件
```bash
npm i babel-plugin-import -D
```
然后就是配置，因为vue-cli3采用的是babel7以上的版本，所以有babel.config.js配置文件，如果你用的是老版本的vue，需要配置的是.babelrc文件
改成下面的样子
```javascript
module.exports = {
  presets: [
    '@vue/app'
  ],
  plugins: [
    ["import", {
      libraryName: 'vant',
      libraryDirectory: 'es',
      style: true
    }]
  ]
};
```
改好了之后，在.vue文件中引用插件时候，需要加以下内容：
template部分：
```javascript
<van-button type="primary">主要按钮</van-button>
```
script部分：
```javascript
import { Button } from 'vant';
```
并且需要在components中注册
```javascript
components: {
  [Button.name]:Button
}
```
这样就可以开始娱乐了。

> 3.自定义组件部分

在vant的api中，有个slot的属性，比如下图这种：
![slotAPI](https://qiniublog.whitedolphin.top/20190705153346.png)

这个属性内的值，可以通过template重写
例如：
```html
<van-cell title="单元格">
  <template slot="icon">
    <img class="new-icon" src="/new/icon/path" />
  </template>
</van-cell>
```
并且重写的样式可以写在当前文件的scope属性的style中。
之前遇到过一个十分傻X的需求，40多个表格按键，不同的图标，不同图标的背景色。
要是每个都写一遍html直接脑壳疼。
其它的样式都还好办，主要是image资源的引用遇到了点麻烦。
直接贴代码吧
```html
<van-grid class="grid-list-btn" clickable square>
  <van-grid-item v-for="item in btnList" :key="item.index" :text="item.text" :to="item.to">
    <template slot="icon">
      <div :style="'background-color:'+item.bg+';'" class="icon-box">
        <img width="20" height="20" :src="require(`@/assets/home/${item.icon}`)" />
      </div>
    </template>
  </van-grid-item>
</van-grid>
```
然后是btnList的数据格式如下：
```JSON
data(){
  return {
    btnList:[
      {text:'第一个按钮',bg:'rgba(121, 149, 222, 1)',icon:'image1.png',to:'/page1'},
      ...此处省略40个Btn
    ]
  }
}
```

差不多这两天就遇到这些事情。其它的也没什么写的必要啦
