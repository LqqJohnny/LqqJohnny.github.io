---
layout: blog
title: 知乎复制粘贴自带版权信息的js代码
date: 2018-01-22 10:52:11
tags: js
---


在知乎、简书、segmentfault等网站上面复制文字如果超过一定长度的话户，在粘贴的时候会发现后面跟着一串版权信息声明。
虽然有的时候比较烦人，但是这对于促进大家重视知识版权有很大帮助，也有利于网站的推广。
<!-- more -->
要是我的博客上也能加上这么一段就好了。于是，在网上查实现的原理 ，发现就是监控了全局的复制（copy）事件，只需要对你想要监控的区域，添加事件监控，在事件处理里面加上自己定制的版权信息就ok了。

代码如下：

```js
function setClipboardText(event){
            event.preventDefault();
            var node = document.createElement('div');
            //对documentfragment不熟，不知道怎么获取里面的内容，用了一个比较笨的方式
            node.appendChild(window.getSelection().getRangeAt(0).cloneContents());
            var htmlData = '<div>著作权归作者所有。<br />'
                            + '商业转载请联系作者获得授权，非商业转载请注明出处。<br />'
                            + '作者：lqq<br />链接：http://lqq.XXXXX<br />'
                            + '来源： XXXX<br /><br />'
                            + node.innerHTML
                            + '</div>';
            var textData = '著作权归作者所有。\n'
                            + '商业转载请联系作者获得授权，非商业转载请注明出处。\n'
                            + '作者：lqq\n链接：http://lqq.XXXXX\n'
                            + '来源： XXXX\n\n'
                            + window.getSelection().getRangeAt(0);
            if(event.clipboardData){  
                event.clipboardData.setData("text/html", htmlData);
                event.clipboardData.setData("text/plain",textData);
            }
            else if(window.clipboardData){  
                return window.clipboardData.setData("text", textData);  
            }  
        };  
        var answer = document.getElementById("answer");
        answer.addEventListener('copy',function(e){
            setClipboardText(e);
        });
```
