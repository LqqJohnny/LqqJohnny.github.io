---
title: cordova的踩坑总结
categories: 移动端
date: 2018-03-23 15:26:44
tags: [cordova,app,移动端]
---

头铁的我再猜了无数坑之后，打算总结一下

<!-- more -->

## cordova

[cordova](http://cordova.apache.org/) 是一个 apach 发布的用于跨平台开发的平台。

cordova 提供了很多原生功能 ，如 相机，麦克风等 具体看[官方文档](http://cordova.apache.org/docs/en/latest/reference/cordova-plugin-battery-status/index.html)

当然还有很多插件可供使用 ： [插件列表](http://cordova.apache.org/plugins/)
、[常用插件](http://www.hangge.com/blog/cache/detail_1158.html)


### 踩坑Start！

接下来直接开始创建 一个 cordova 项目

#### 安装cordova

- npm 安装

    使用 npm 全局安装好 cordova ，`npm install cordova -g`

- yarn 安装
    也是全局安装 `yarn global add cordova`
    （安装完之后，如果运行 cordova 指令不存在，加一个path环境变量，例: `C:\Users\lqq\AppData\Local\Yarn\bin` ）

> 注意： 由于npm版本的问题 cordova 在后面创建项目时可能会报错 ， 若遇到，可换成 yarn 安装,在加上环境变量即可


#### 创建项目

一般创建项目可以直接 `cordova create projectName`

但是，在创建的时候可以指定**模板**， 可以为以后的开发省下很多事。

以我之前写的项目为例 ，使用的模板是 ： [cordova-template-framework7-vue-webpack](https://github.com/caiobiodere/cordova-template-framework7-vue-webpack)

就是 cordova + f7 + vue +webpack , f7 相当于是一个组件库
当然还有很多其他的模板 ，之后补充。

创建步骤：

```bash
#指定模板
cordova create example com.example.app example  --template cordova-template-framework7-vue-webpack

```

这样就创建完成了！

#### 运行项目

运行的指令是  `cordova run browser` 这个 browser 表示运行在浏览器 ，如果连接了手机或者模拟器 可以换成 ios 或者 android

不过首先需要添加 平台（platform）支持

```bash
# 添加平台 根据需要添加
cordova platform add browser [,ios] [,android]

# 运行 -- --lr可实时更新修改的文件  live-reload的缩写
cordova run browser -- --lr

```
添加 platform 这一步可能比较久 因为要安装一些 依赖 ，使用的 npm 当然会比较慢了，不过也有办法 ，把npm的默认资源地址 换成 淘宝镜像地址，或者使用这个 [nrm](https://github.com/Pana/nrm) 来快速切换资源地址。

```bash

nrm ls

    * npm -----  https://registry.npmjs.org/
      cnpm ----  http://r.cnpmjs.org/
      taobao --  https://registry.npm.taobao.org/
      nj ------  https://registry.nodejitsu.com/
      rednpm -- http://registry.mirror.cqupt.edu.cn
      skimdb -- https://skimdb.npmjs.com/registry

nrm use taobao

    Registry has been set to: https://registry.npm.taobao.org/

```

好了 现在项目应该运行起来了，看到的是一个 简单的demo页面 ， ，接下来就可以熟悉下[项目结构](https://github.com/caiobiodere/cordova-template-framework7-vue-webpack#features) 然后开始开发了。


#### 添加插件
查看插件列表： `cordova plugin ls`

添加插件 ： `cordova plugin add cordova-plugin-camera`

删除插件 ： `cordova plugin rm cordova-plugin-camera`

更新插件： `cordova plugin update`


[插件列表](http://cordova.apache.org/plugins/)
