---
title: js中foreach无法使用break跳出循环
categories:
  - 笔记
pageview: 1
date: 2019-06-17 13:48:52
tags:
  - js
cover: https://qiniublog.whitedolphin.top/234.jpg
---
```html
如果遇到此问题，那么很遗憾，确实不能使用break跳出foreach循环。
但我们可以使用try...catch通过throw new Error来跳出循环。
```
> 比如数组:
```json 
arrayList=[
  { id:"1", name:"Tom"},
  { id:"2", name:"Lily"},
  { id:"3", name:"John"},
  { id:"4", name:"Jerry"}
]
```
> 遍历数组，当id === "3"的时候跳出循环
```js
try{
  arrayList.foreach(item => {
    if(item.id === "3"){
      throw new Error(item.name);
    }
  })
  console.log("something wrong?")
}catch(e){
  return console.log(e.message)
}
```
> 打印出来的log将会是 John
```bash
John
>_
```
