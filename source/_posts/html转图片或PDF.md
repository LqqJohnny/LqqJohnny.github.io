---
title: html转图片或PDF
date: 2018-03-05 13:55:43
tags: [node,pdf]
---

今天带来一个好玩的网页截图功能 ，就是将网页转换成一张图片 或者 PDF

<!-- more -->
刚开始发现网上有很多 方案 ，例如 ： http://blog.csdn.net/younglao/article/details/77746039

这里介绍了三种：

- html-pdf 模块
- wkhtmltopdf 工具
- phantom 模块

这三种方法各有优缺点，视情况选择。

其中我用过 phantom ，所以我就试着用它来实现该功能 。

实现的代码还是挺少的 ，我使用了 express 做一个服务端， 通过调用接口 /html2img 来将 指定的网址 ： https://www.baidu.com/ 转成了 pdf ，效果如图：

![html2pdf](/images/html2pdf.png)


代码如下：

```js
// html 转为 pdf 或者 图片
router.get('/html2img', async function(req, res, next) {

     var url = "https://www.baidu.com/";
     var path = "./public/snapshot/shot.pdf"; // 将后缀名改为 png 即可保存为图片
     var ph = await phantom.create();
     var page = await ph.createPage();
     var status = await page.open(url);
     page.property('viewportSize',{width: 1000, height: 500}); //设置视窗的大小
     await page.render(path)
     console.log('Page rendered');
     ph.exit();
     res.sendfile(path);

});

```

上面网址给出的例子 使用大量的回调函数，代码看起来极为负责 ，这里我使用 `async` 和 `await` 将代码简化，更加清晰，便于理解。

page.render() 生成文件之后，便可以使用 res.sendifle() 来发送文件或者使用 res.download() 在客户端唤起下载器 下载该pdf 。

> end
