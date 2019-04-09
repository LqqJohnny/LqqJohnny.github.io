---
title: node生成图片验证码
date: 2018-02-23 11:38:30
categories: node
tags: [node,验证码]
categories: node
---

网上一搜 `node生成验证码` 会看到很多人的回答，大多数都是 基于C++的，例如 ccap，node-canvas 这些npm包安装的时候需要使用 `node-gyp`编译，这个过程很是复杂，经过了各种报错，我已经放弃了，转而寻找一些不是C++的包，最终找到了这个 ： [svg-captcha](https://github.com/lemonce/svg-captcha)

<!-- more -->

svg-captcha 是基于 svg 的，直接用字符串转为 svg 路径，没有 text 标签 ，所以无法从svg中直接获得验证码 ，比较安全。

### 使用方法

可以设置 背景颜色，线条数量 ，颜色，字体，图片大小，字数等
具体用法可直接参照 svg-captcha 的 [中文文档](https://github.com/lemonce/svg-captcha/blob/master/README_CN.md)
文档中有详细介绍和使用例子。

### 例子

![例子1](/images/yzm1.png)

![例子1](/images/yzm2.png)
