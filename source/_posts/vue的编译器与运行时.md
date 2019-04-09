---
title: vue的编译器与运行时
categories: vue
date: 2018-08-16 10:56:07
tags: [vue,complier,runtime]
---

对于vue官方文档上的 complier和runtime的解释，看的有点云里雾里。

<!-- more -->

在官网上对于 vue 所有的构建版本有很详细的说明了

https://vuefe.cn/v2/guide/installation.html#不同构建版本的解释说明

大致分为完整版和运行时版

 > 运行时版 + 编译器 = 完整版

也就是当你不需要编译器的时候尽量选择运行时版 这样可以节省很多时间 最后打的包也能体积小一点。

- 什么是运行时？

 运行时负责创建 Vue 实例(creating Vue instances)、渲染(rendering)和修补虚拟 DOM(patching virtual DOM) 等的代码

- 什么是编译器？

  负责将template 编译成 render函数 进而让运行时使用。

- 什么时候需要用编译器？

  如果你的vue中用了template属性 就应该引入complier否则会报错 ，例如，以直接引入script的方式使用vue ，在组件的初始化代码里你使用了 template属性或者在html使用了vue的模板指令 就需要编译器，
  用vue-cli+webpack 创建的项目就不需要compilier ，这是官方推荐的，因为complier的事已经在webpack中的vue-loader做完了，所以打包完之后已经全是js代码 不需要在进行编译了。


总而言之，使用script的时候使用完整版本， 使用webpack的时候使用运行时版
