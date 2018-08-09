---
title: 实现一个once函数
date: 2018-07-16 16:18:54
tags: [js]
---
经过once处理的函数，只能调用一次
<!-- more -->

```js
function once(func){
			var hasHandled = false;
			return function(){
				if(!hasHandled){
					func.apply(null,arguments);
					hasHandled = true;
				}
				return undefined;
			}
		}

		function say(words){
			console.log(words);
		}

		var newfun = once(say);
		newfun("hello");
		newfun("world");
		newfun("!");

    // hello
```
