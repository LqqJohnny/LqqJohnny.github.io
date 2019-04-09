---
title: vue中如何实现跨域
date: 2018-08-14 13:07:33
categories: vue
tags: [vue,跨域]
---

在 vue 开发中，可能会遇见一些跨域的问题，例如后端给的接口地址的域名与我们本地开发时的localhost 不同域，而后端的接口是已经发布了的生产环境的地址，显然我们无法要求后端修改地址了，那只好自己找办法解决了。

<!-- more -->

- 方法一 ： proxyTable

这个proxyTable 其实是webpack的配置 ，在config/index.js 中找到 proxyTable属性 进行配置。

 步骤 ：
 1. 先在本地开启一个服务器，与vue项目不同端口，并写定一个接口 ，模拟远程跨域服务器 ：例如 `localhost:8080/hello`
 2. 设置 proxyTable属性

 ```js
 proxyTable: {
   '/': {  // 服务器接口前缀 如果没有则设置为 ‘/’，如果为www.XXX.com/api/*** 则设置为 /api
     target: 'http://localhost:8080/',//跨域的地址
     changeOrigin: true,//是否跨域
     pathRewrite: {
       '^/api': '/api'//需要rewrite重写
     }
   }
 },
 ```
 3. 把项目运行在 8000 端口 ，在vue页面中请求接口
```js
this.axios.get("/hello").then((response) => {
  console.log(response.data)
})
```


按照以上步骤 即可实现跨域，不信？ 那你把proxyTable去掉 你会发现请求 是404 ，然后再把 vue页面中的地址修改 为
`http://localhost/8080/hello` 这个时候将会提示 跨域。
