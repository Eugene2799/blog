---
title: vue2父子模版组件之间的传值
categories:
  - 笔记
pageview: 1
date: 2019-06-17 13:50:28
tags:
  - vue
cover: https://qiniublog.whitedolphin.top/20190705144656.png
---

```html
Vue2父组件给子组件传值通过定义props来传值
子组件给父组件传值则使用this.$emit(eventName, [...args])来触发父组件定义的方法
```
## 1. 父组件传值给子组件
> 子组件先定义好需要父组件传的值及如何使用
- 文件名TestFatherToSon.vue
```vue
<template>
  <div>{{this.fatherTellMe}}</div>
  <div>{{content}}</div>
</template>

<script>
  export default {
    name: "TestFatherToSon",
    props: {
      fatherTellMe: {
        type: String,
        required: true
      },
      fatherControl: {
        type: Boolean,
        required: true
      }
    },
    data() {
      return {
        content: ""
      }
    },
    method: {
      controlDiv() {
        if (this.fatherControl) {
          this.content = "父模版传了True"
        } else {
          this.content = "父模版传了False"
        }
      }
    }
  }
</script>
```
> 父组件调用子组件需要定义参数
- 文件名FatherToSon.vue
```vue
<template>
  <div>
    <test-father-to-son :father-tell-me="word" :father-control="control"></test-father-to-son>
  </div>
</template>

<script>
  import TestFatherToSon from "./TestFatherToSon";

  export default {
    name: "FatherToSon",
    components: {TestFatherToSon},
    data() {
      return {
        word: "父模版给你的内容",
        control: true
      }
    }
  };
</script>
```
---
## 2. 子组件传值给父组件
> 父组件调用子组件，并定义好触发的事件
- 文件名SonToFather.vue
```vue
<template>
  <div>
    <div>{{word}}</div>
    <test-son-to-father @changeText="changeMethod"></test-son-to-father>
  </div>
</template>

<script>
  import TestSonToFather from "./TestSonToFather";

  export default {
    name: "SonToFather",
    components: {TestSonToFather},
    data() {
      return {
        word: "这里是父模版的内容"
      }
    },
    methods: {
      changeMethod(val) {
        if (val && val.type === "change") {
          this.word = val.content;
        } else {
          console.log("error...")
        }
      }
    }
  };
</script>
```
> 子组件组装好传值内容，使用this.$emit()来触发父组件的事件
- 文件名TestSonToFather.vue
```vue
<template>
  <div @click="putDataToFather">点我传值给父组件</div>
</template>

<script>
  export default {
    name: "TestSonToFather",
    data() {
      return {
        content: ""
      }
    },
    method: {
      putDataToFather() {
        let data = {
          type: "change",
          content: "子组件传给父组件的值"
        };
        this.$emit("changeText", data);
      }
    }
  }
</script>
```

这样的套路看明白了吧。
