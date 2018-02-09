---
title: github的小徽章
date: 2018-02-09 10:46:44
tags: github
---
前几天折腾 github 的 readme 文档真的烦死了，好在昨天弄好了，今天写一下，记录一下坑。
<!-- more -->
先看看效果吧 ==> [戳这](https://github.com/LqqJohnny/VBlog)

我一开始看到这个就会想这有什么难的  ，就用markdown语法引入几张图片而已嘛！

事实并非如此，这里面的坑多着呢！

### 图标来源

有些图标是和项目实际情况同步的 ，例如 下载数量 、 build status ，

<img src="https://camo.githubusercontent.com/514abce7f4f4e57ca8e353bccf57968572abe342/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f646d2f7675652e737667">

这个是真实的实时反映下载量的，所以肯定要和项目联系起来。还有build status：

<img src="https://camo.githubusercontent.com/07ce5da29c49e8b5c6ef00d818d2c3d38c04fa55/68747470733a2f2f696d672e736869656c64732e696f2f636972636c6563692f70726f6a6563742f7675656a732f7675652f6465762e737667">

这些就要用到 [shields.io](http://shields.io/) 了,复杂一点的图标可能还会用到 [travis-ci](https://travis-ci.org/) 等工具

下面是几篇 图标用法文章：

- https://lpd-ios.github.io/2017/05/03/GitHub-Badge-Introduction/

- https://www.liaoxuefeng.com/article/0014631488240837e3633d3d180476cb684ba7c10fda6f6000

travis 使用时需要注意的是 ：
  - 必须要对应你的项目
  - travis 配置文件必须要上传，且要注意传到的分支，使用时要带上分支名

### 怎么使用

使用的过程很是抓狂。

如果用 `shields.io` 推荐的方法，
```
[![CocoaPods](https://img.shields.io/cocoapods/l/AFNetworking.svg)]()
```
可以引入 ，但是不能让他居中 为了让他居中，还需要加上html标签

``` html
<p align='center'>
[![CocoaPods](https://img.shields.io/cocoapods/l/AFNetworking.svg)]()
<!-- 如果是多个图标的话 中间不能用换行，否则markdown会解析为 p 标签破坏原有结构 导致强行换行 -->
[![CocoaPods](https://img.shields.io/cocoapods/l/AFNetworking.svg)]()
</p>
```

现在传上github试试看。。你可能会发现怎么没有图标变成了一串 文字， markdown的语法没有解析 这还是 html标签的问题。

所以，涨了个姿势： md 如果用了 html标签 它里面就尽量不要写md 语法了，要写就也写 html 。

换成下面这样 ：
```html
<p align="center">
  <a href="https://travis-ci.org/LqqJohnny/VBlog"><img src="https://travis-ci.org/LqqJohnny/VBlog.svg?branch=develop" alt="Build Status"></a>
  <a ><img src="https://img.shields.io/badge/language-javascript-yellow.svg" alt="Build Status"></a>
</p>
```

这样在上传一下就 ok了。

### 总结


装逼是要付出代价的。
