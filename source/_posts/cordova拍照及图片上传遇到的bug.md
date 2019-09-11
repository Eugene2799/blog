---
title: cordova拍照及图片上传遇到的bug
categories:
  - 笔记
pageview: 1
date: 2019-09-12 00:33:28
tags:
cover:
---

> 前言：

```text
  听着《It will rain》，现在已经是凌晨1点钟。
  想着刚工作的那一年，看学长天天夜里凌晨记录一天的笔记。自己也会跟着记一些，却从来没坚持下来过。
  说不上来的感觉。配上糟糕的一天，只觉得无力。
  记录一下今天遇到的问题吧。至少今天学习了
```

> 经过

- 项目马上验收了，下午说为啥群聊没有拍照拍视频，而是选择手机里拍好的照片和视频发。然后就和项目经理撕逼，当时说的需求就是要发图片视频文档什么的blabla，吵得很尬，于是闷头把拍照做了。本以为会很简单，但是过程挺曲折的。也学习了一下file对象、blob对象、cordova的fileEntry什么的。所以记录记录。
- 其实功能不难，最后的代码行数也不多，主要是发现和解决问题的过程很曲折，做了很多的尝试。
- 关键点有以下几点：
  - 1.获取拍照后的照片base64编码；
  - 2.使用base64编码转换到blob对象；
  - 3.cordova项目安装了file插件后，会覆盖html的file方法的对象
  - 4.使用blob对象转换到file对象
  
   ```html
    关键点3.是耗时的关键，因为后端接口写得上传数据类型只能是file类型，所以很容易有误导。
    发现的过程就是在调试的时候发现原本html的new File()方法的api应该是File(array[],filename,..options),而调用的时候发现new File()的api变成了File(name，.... blabla....)，是因为cordova-plugin-file插件安装后覆盖了html的File()和window.file()方法。 F**k！
    ```

> 解决关键点1的代码

```html
  <img id="myImage" src="">
  <i @click="takePicture($event)" class="fa fa-camera"></i>
```

```javascript
takePicture(event) {
  let self = this

  function onSuccess(imageURI) {
    var image = document.getElementById('myImage');
    //通过新建canvas元素获取base64转码
    var canvas = document.createElement('canvas'),
        ctx = canvas.getContext('2d');
    image.crossOrigin = 'Anonymous';
    image.onload = function () {
      canvas.height = image.height;
      canvas.width = image.width;
      ctx.drawImage(image, 0, 0);
      var dataURL = canvas.toDataURL('image/jpeg');
      //获取base64的dataURL数据，并调用上传
      self.updateImage(dataURL);
      canvas = null;
    };
    image.src = imageURI;
  }

  function onFail(message) {
    console.log('Failed because: ' + message);
  }

  cameraTakePicture(onSuccess, onFail)
}
```

```javascript
export function cameraTakePicture(successCallback, errorCallback) {
  return navigator.camera.getPicture(successCallback, errorCallback, {
    quality: 50,
    destinationType: Camera.DestinationType.FILE_URI
  });
}
```

> 解决关键点2的代码

```js
updateImage(imageURI) {
  //将base64转为blob类型数据
  var myBlob = dataURLtoBlob(imageURI)
  //将blob类型数据转为file类型数据
  var myFile = blobToFile(myBlob, 'testimg.jpg');
  //以下代码省略，为调用post方法发送数据
  ...
}
```

```js
export function dataURLtoBlob (dataurl) {
  // 转为blob数据类型，如文件分块上传等相关，有很多可以优化图片的方法
  var arr = dataurl.split(','),
      mime = arr[0].match(/:(.*?);/)[1],
      bstr = atob(arr[1]),
      n = bstr.length,
      u8arr = new Uint8Array(n);
  while (n--) {
    u8arr[n] = bstr.charCodeAt(n);
  }
  return new Blob([u8arr], { type: mime });
}
```

> 解决关键点3、4的代码

```js
export function blobToFile(theBlob, fileName){
  // 转为file类型数据
  // file类型数据继承自blob数据类型，搜到的大多数内容都是说多一个name和lastModifiedDate，其实还有一个二进制类型的转换，就是formdata。否则后端识别的还是blob类型
  const formData = new FormData();
  theBlob.lastModifiedDate = new Date();
  theBlob.name = fileName;
  // 通过formData.set(key, blobData, blobName)转换为File类型
  formData.set('a', theBlob, fileName);
  return formData.get('a');
}
```

> 其它后记：

```text
还接触到了fileEntry和文件存储这部分的内容。
所以搞东西还是盲区学习的多，横冲直闯，进行很多的尝试，学习到很多扩展和意外的内容。
```
