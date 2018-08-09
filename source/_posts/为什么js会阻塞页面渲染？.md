---
title: 为什么js会阻塞页面渲染？
date: 2018-07-24 14:31:23
tags: [线程,js]
---

经常会碰到这种情况，当出现一个死循环，整个页面会变得很慢很卡，甚至有时直接提示页面崩溃。大家都是知道这是不当js语句导致的页面阻塞。但是你知道为什么会阻塞吗？

嘿嘿 ， 其实这和线程有关。

<!-- more -->

### 线程和进程

打开一个浏览器，就会开启一个进程，在打开一个tab页 ，又会多出一个进程，在任务管理器中可看到。一个浏览器可创建多个进程 ，一个进程又可以创建多个线程。

### 线程

一个页面有哪些线程呢？

- GUI渲染线程
- JS引擎线程
- 事件触发线程
- 定时触发器线程
- 异步http请求线程

在这些线程中有些是互斥的

- 渲染线程和JS线程

  两个任意一个在运行的时候，另一个都是处于等待状态。倘若不是的话，假如js修改了一个元素的属性，但是渲染进程还没渲染到该元素，那js修改属性就会失败。所以把他们定为互斥是很有必要的。

  所以也就有了上面所说的页面阻塞问题，因为js线程一直卡死未结束，那渲染线程将会一直等待，所以为了避免阻塞，js代码一定要避免执行时间长，而且也推荐将js代码放在html代码之后，以防止阻塞渲染。

  > 所说的js是单线程，是指JS引擎线程是单线程的，事实上浏览器是多线程的。


  - 异步的实现
  在js中有一些异步操作的实现，例如使用 setTimeout， setInterval ，http请求...
  这些都是在另外一个线程上，这样才不会阻塞JS引擎进程。当JS引擎线程运行到 setTimeout，鼠标点击，ajax 这样的代码时，它会将对应的任务添加入 事件线程。待对应的事件符合触发条件了，事件线程再将事件添加到JS引擎线程的 eventloop 队列。
  待JS引擎线程的主线上的任务处理完了，就会从eventloop中读取处理。

```js

console.log(1);

setTimeout(function(){
  console.log(2)
})

let p = new Promise((resolve)=>{
  console.log(3);
  resolve();
}).then(function(){
  console.log(4)
})

// 1 3 4 2
```

> 并不是按照先后顺序读取事件，而是按照种类来区分顺序的，script(主程序代码)—>process.nextTick—>Promises...——>setTimeout——>setInterval——>setImmediate——> I/O——>UI rendering  参考：https://github.com/forthealllight/blog/issues/5

#### 多线程？

在H5中 ， 增加了一个 web Worker ，这就是 目前比较火的PWA技术的基础，PWA是基于serviceWorker 。 webWorker 是 JS引擎线程开了一个子线程，worker的内容运行在其中，其运行不会影响主线程的任何东西，但是主线程可以完全控制子线程，其中的代码也不会阻塞主线程的运行。
