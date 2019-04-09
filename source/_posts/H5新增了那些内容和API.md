---
title: H5新增了那些内容和API
date: 2019-04-09 14:48:55
categories: 面试
tags: [H5]
---

### 面试系列--H5新增的内容

<!-- more -->

#### 语义化标签
在展现上没有变化，在编辑上更利于理解，也利于日后的维护

#### 自定义属性

可以在元素上自定义属性 ：`data-name` ，然后可以在css中或者js中使用，
js中取用方法：
```js
element.dataset ["name"]
```

#### 选择器

document.querySelector()和document.querySelectorAll()方法

这两个方法类似于 **Jquery** 的选择器，只需要传入css选择符，就能返回匹配的元素，*querySelector* 只会返回第一个找到的，
*querySelectorAll* 会返回所有匹配元素。

#### calssList

还记得以前用原生 js 给元素增删 class的痛苦吗？做字符串拼接是很low的事，所以在H5中新增了一个 calssList 属性，
他自带了 add 方法和 remove 方法，来增删类名。classList 是一个类数组结构 ，但它不是数组。
```js

classList instanceof Array  === false 
```
除此之外，classList 自带的api方法还有很多：
```
add remove  contains forEach replace toString toggle 
```

#### 全屏
和按键`F11`的效果是一样的 

#### 页面可见性
根据 `visibilitychange` 事件，可以在页面切换的时候，暂停音视频播放等操作。
需要做出 对应浏览器的适配

#### H5预加载
可以对一些静态资源进行预加载处理，可跨域。
```html
<!-- 不管什么类型资源，都是用link标签来声明预加载 -->
<link rel="prefetch" href="page2.html" />

<!-- dns-prefetch 的兼容性更好一点 -->
<link rel="dns-prefetch" href="http://example-domain.com/">
```
不是所有资源都可以预加载的，不可缓存的资源不建议预加载，否则也只是浪费资源。

#### canvas 和 svg
canvas 位图 使用js绘图，
svg 矢量图 使用xml绘图


#### 多媒体
``` html
<video> 视频
<audio> 音频
<source>
<track>
```
#### 拖拽
H5现在支持原生的拖拽事件。
```
ondragstart       开始被拖动的时候触发
ondrag            拖动的过程总被连续触发
ondragend         停止拖动的时候的触发
ondragenter       当被拖拽的元素进入到目标元素
ondrageover       当被拖拽的元素在目标元素内移动
ondrageleave      当被拖拽的元素离开目标元素  绑定给目标元素
ondrop            在目标元素内停止拖拽  绑定给目标元素

```

#### 文件API

FileReader 

#### web本地存储 
localStorage  sessionStorage 

> 待补充