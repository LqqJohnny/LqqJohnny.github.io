---
title: hexo展示其他项目example
date: 2018-02-01 17:12:46
tags: [hexo]
categories: hexo
---
用过hexo的人可能会发现，在`github`上使用`gitpage`之后,其他项目就无法使用`gitpage`了，因为`github`限制了每个人智能开一个`gitpage`，但是水来土掩，换个方式，就可以解决这个问题了。

<!-- more -->


> 本文水分比较多 ，懒得看的，直接看最后总结找解决方法


### 更简单的方法？

其实有很多方法 ，比如再创建一个账号 ，一个专门用来展示 demo的页面，或者利用其它网站 codepen 码云 ... 等网站来展示demo，

但，这里分享一个更简单的方法 。

### 探究过程
首先 ，接受只能有一个gitpage的事实 ,gitpage是怎么实现的呢 ，稍微看下hexo上传之后的目录  你会发现 `XXXX.github.io` 对应的就是你的hexo项目上传的代码里面的`index.html`

那如果我把 我的demo 放在他的目录里面一起传上去例如 ： `/demo/index.html` 这样不就可以了吗？

来 尝试一下

把文件夹复制进`.deploy_git`文件夹  ，再 `hexo g -d` , 等待

**失败**
打开 `.deploy_git` 一看，刚才复制进去的文件夹不见了 ，回头看看 `hexo d` 执行的结果 ，第一句就是 clear .deploy_git 也就是每次deploy 都会把里面内容都删除 重新打包，所以我们刚加的文件夹被删了。

有什么办法呢 ，再看看   `hexo d` 的执行输出语句，还有一句

`Copying files from public folder...`

对了 就是这句，把demo放进public文件夹试试？

- `/demo/indx.html`复制进public
- hexo d

查看 `.deploy_git` demo文件夹有了
在打开网址  `XXXX.github.io/demo/index.html` 看看  
**成功**


### 总结
放在public文件夹里面的文件不会被删除，会原样的上传至github，通过对应的路径即可访问自己的静态html文件。
```
--public
    |___ demo   // 加入此文件夹
          |___ index.html


访问地址 : https://你的名字.github.io/demo
```
