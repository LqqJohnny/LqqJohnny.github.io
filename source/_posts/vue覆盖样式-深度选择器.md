---
title: vue覆盖组件样式-深度选择器
date: 2018-07-11 14:55:56
tags: [vue,深度选择器]
---


> 背景

用 vue 开发时会用到一些组件库，例如比较流行的 elementUI ，iView ， museUI ...
但是在使用中 有时需要修改组件库自带的样式，这时可能会写在一个公共的css文件，然后在main.css中引入，这确实是可行的 ，但如果样式很多，那每个页面都会加载很多不必要的样式。 况且，一个页面的css 写在 不同的 css文件里面 很不利于维护。这里推荐一个css的属性  **深度选择器** .

<!-- more -->

先来一段代码感受一下

例子 ：
```js

<template>
  <div id="app">
    <el-input  class="text-box" v-model="text"></el-input>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      text: 'hello'
    };
  }
};
</script>

<style lang="less" scoped>
.text-box {
   input {
    width: 166px;
    text-align: center;
  }
}
</style>

```
在这个例子里面 ， input{...} 是无效的。

现在使用 深度选择器 试试 ：

```js

<template>
  <div id="app">
    <el-input v-model="text" class="text-box"></el-input>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      text: 'hello'
    };
  }
};
</script>

<style lang="less" scoped>
.text-box {
  /deep/ input {
    width: 166px;
    text-align: center;
  }
}
</style>

```

ok ! 这样是有效的 。
/deep/ 是less 的写法 ，如果使用的事css的话，就替换成 `>>>`


在vue 中 为什么使用scoped的组件的css是局部有效呢 ？ 打开控制台，不难发现 ，css 被编译成了类似这样：

```css
.a[data-v-f3f3eg9] .b { /* ... */ }

```

而elementUi组件库里面的组件是不带有这个的， 不用深度选择器的话 ，原先的编译后css是：
```css
.text-box input {
    width: 166px;
    text-align: center;
}

```
所以没有匹配到对应的 input

用了/deep/之后呢 ：

```css
.text-box[data-v-f3f3eg9]  input {
    width: 166px;
    text-align: center;
}
```
这样 就能匹配到了。
