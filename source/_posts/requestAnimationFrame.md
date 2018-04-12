---
title: requestAnimationFrame
date: 2018-03-06 10:39:05
tags: [requestAnimationFrame,动画]
---

requestAnimationFrame 之前没用过 今天在网上copy了一段代码 ，之前一直知道这个东西，没接触到所以没去了解。

<!-- more -->

我还是比较想了解 它 的用法和原理。

### 原理

浏览器每次重绘，就会通知 requestAnimationFrame ，通过在requestAnimationFrame 的回调方法中在 通知一次requestAnimationFrame 就可以达到 动画的效果了。

### 用法

```js
  function drawFrame() {
   // animation code...
   window.requestAnimationFrame(drawFrame);
  }
  window.requestAnimationFrame(drawFrame);
```

### 优点

- 高效
  一般的js动画可能会因为 事件队列里面 settimeout 前面有很多其他的 导致动画时间的不准确， 而 requestAnimationFrame 不会。

- 节省资源
  页面最小化后  不会发生重绘 ，所以不会有动画，节省了不必要的资源开销

### 兼容性封装


```js
(function() {
 var lastTime = 0;
 var vendors = ['ms', 'moz', 'webkit', 'o'];
 for(var x = 0; x < vendors.length && !window.requestAnimationFrame; ++x) {
  window.requestAnimationFrame = window[vendors[x]+'RequestAnimationFrame'];
  window.cancelAnimationFrame = window[vendors[x]+'CancelAnimationFrame'] || window[vendors[x]+'CancelRequestAnimationFrame'];
 }

 if (!window.requestAnimationFrame)
  window.requestAnimationFrame = function(callback, element) {
   var currTime = new Date().getTime();
   var timeToCall = Math.max(0, 16 - (currTime - lastTime));
   var id = window.setTimeout(function() { callback(currTime + timeToCall); },
    timeToCall);
   lastTime = currTime + timeToCall;
   return id;
  };

 if (!window.cancelAnimationFrame)
  window.cancelAnimationFrame = function(id) {
   clearTimeout(id);
  };
}());
```

这是一个简单的 hack ，里面还封装好了  cancelAnimationFrame 方法 用于取消动画 ，类似于 clearTimeoout 。
