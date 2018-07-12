---
title: vue骨架屏的应用
date: 2018-07-12 14:40:13
tags: [vue,骨架屏]
---

骨架屏就相当于占位图 ，在真实内容还未加载完之前，用于展示在页面上，加载完之后，用真实内容代替它，这样有利于用户体验，避免看到一块空白页面。

<!-- more -->

### 原理

vue中骨架屏利用的事 vue的 slot ，该指令会在其父元素内无内容时展示，避免其父元素中无东西可展示，这功能正和我们的需求符合。

### 简单应用

利用上面说道的 slot 我们可以简单的实现一个骨架屏。

1. 首先创建一个 骨架的vue组件

例如 ：
```js
// ------- artContentSkt.vue -----

<template lang="html">
  <div class="artContentSkt">
    <div class="line short"></div>
    <div class="line "></div>
  </div>

</template>

<script>
export default {
}
</script>

<style lang="css" scoped>

.artContentSkt{
  padding: 2rem;
}
.artContentSkt .line{
  height :0.5rem;
  width: 10rem;
  background-color: #def6fe;
  margin: 0.5rem 0;
  border-radius:0.5rem;
  position: relative;
}
.line.short{
  width: 7rem;
}
</style>
```
在这个骨架vue中，只需要简单的静态页面，不需要事件。

2. 然后在需要的地方引入并使用：

```js

<vue-lazy-component :timeout="500" class="lazyComponent"  @after-leave="afterInit" :class="{'done' : slideStart}">
  <article class="article" id="article" v-html="articleContent" v-highlight v-if="!passErr">
  </article>

  <artContentSkt slot="skeleton"></artContentSkt>
</vue-lazy-component>

```
这里还需要一个插件 [vue-lazy-component](https://juejin.im/post/59bf501ff265da06602971b9) 用于懒加载组件的


3. 这时候打开页面，就能看到先看到的事 骨架vue的内容，然后一会儿过后，才是真实内容。

### 高大上的实现

接下来还有一种便于维护的方法。

首先，我们需要知道页面的渲染时发生了什么事，在所有东西渲染之前，页面是模板文件 index.html 上的内容，然后当js执行完了，div#app中的内容会被全部替换。

那在这里我们就可以通过修改模板文件来实现骨架屏了。

当然你可以直接手动写死一些骨架内容到div#app中。但是更推荐自动化且扩展性强的方法。既然我们用的是vue，那为什么不把骨架写成一个vue组件呢。

#### 创建skeleton组件

skeleton的内容还是用上面的例子吧。

#### 入口文件
```js
// ----- skeleton.entry.js -----

import Vue from 'vue'
import Skeleton from './Skeleton.vue'

export default new Vue({
  components: {
    Skeleton
  },
  template: '<skeleton />'
})

```

#### vue-server-renderer 服务端渲染

现在有了 vue 组件 ，接下来就是 将skeleton 转成 html 并插入到 index.html  需要用到一个 webpack 插件 ： [vue-server-renderer](https://www.npmjs.com/package/vue-server-renderer) ，一个服务端渲染的插件

新建一个  webpack.skeleton.conf.js
```js
const path = require('path')
const webpack = require('webpack')
const nodeExternals = require('webpack-node-externals')
const VueSSRServerPlugin = require('vue-server-renderer/server-plugin')

module.exports = {
  target: 'node',
  entry: {
    skeleton: './src/skeleton.entry.js'
  },
  output: {
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist/',
    filename: '[name].js',
    libraryTarget: 'commonjs2'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          'vue-style-loader',
          'css-loader'
        ]
      },
      {
        test: /\.vue$/,
        loader: 'vue-loader'
      }
    ]
  },
  externals: nodeExternals({
    whitelist: /\.css$/
  }),
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js'
    },
    extensions: ['*', '.js', '.vue', '.json']
  },
  plugins: [
    new VueSSRServerPlugin({
      filename: 'skeleton.json'
    })
  ]
}
```
接下来， 命令 `webpack --config ./webpack.skeleton.conf.js` 可以将 skeleton.vue 转成一个 skeleton.json 了，给vue-server-renderer使用

#### 插入代码

有了skeleton.json 就可以插入index.html了 。

根更目录 新建 skeleton.js
```js
const fs = require('fs')
const { resolve } = require('path')

const createBundleRenderer = require('vue-server-renderer').createBundleRenderer

// 读取`skeleton.json`，以`index.html`为模板写入内容
const renderer = createBundleRenderer(resolve(__dirname, './dist/skeleton.json'), {
  template: fs.readFileSync(resolve(__dirname, './index.html'), 'utf-8')
})

// 把上一步模板完成的内容写入（替换）`index.html`
renderer.renderToString({}, (err, html) => {
  fs.writeFileSync('index.html', html, 'utf-8')
})

```

> 注意： 在模板文件index.html 中的div#app内部加上 <!--vue-ssr-outlet--> 注释，vue-server-renderer就会把骨架内容插入到这里。

接下来 运行 `node skeleton.js` 就可以看到骨架插入index.html成功！


### 总结

最后，对上面的命令进行整合 ， 可以再 package.json 中将  `webpack --config ./webpack.skeleton.conf.js` 和 `node skeleton.js` 只需要执行 npm run skl 即可快速生成 骨架。

这种自动化可扩展的方法虽然前期有点费事，但是很利于项目的维护，至少它还能装逼嘛！


参考 ： https://segmentfault.com/a/1190000014832185
