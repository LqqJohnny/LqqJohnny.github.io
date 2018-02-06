---
title: 压缩js和css的命令行工具
date: 2018-02-05 15:02:51
tags: [js]
---

这是一个压缩代码的小工具，js 和 css 都可以压缩 且可批量压缩 执行 `ug src dest` 就可以将src里面的所有js 和css 压缩到 文件夹 dest。

<!-- more  -->

## 源码
该工具的源码在 [这里](https://github.com/LqqJohnny/uglify-tool)

## 使用
```bash
#全局安装
npm install uglify-tool -g
# 批量压缩
ug src dest
#单个压缩
ug main.css target.css
#单个压缩
ug main.css dest/main.css

```
## 优缺点
优点： 使用方便，可多文件压缩，也可单文件压缩 ，也可多层次压缩
缺点： 代码粗糙，错误提示不完整

## 原理
1. 根据命令行输入的 第2和第3个参数 ，确定输入和输出，在判断输入输出的路径是 文件 还是文件夹，在做出不同的处理
2. 如果是 文件 ，直接压缩。
3. 如果是文件夹，则遍历文件夹下的文件 ，逐个压缩，碰到文件加 则用递归重复以上步骤。

## 总结
在日常开发中，如果不是使用webpack，grunt等可自动化的工具的时候，想要去压缩你的js和css文件 试衣间比较麻烦的事，例如一些古老的 jq 项目，javaweb项目。

这时，你可能需要新建一个grunt 或者 webpack ，再安装依赖什么的 ，这就很麻烦了，还会给项目里面加上一些不必要的文件 如： node_modules package.json webpack.conf.js ......

而这个工具就可以解决上面的麻烦，简单一句 cmd 即可。
