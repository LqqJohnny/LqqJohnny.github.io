---
title: js位运算符的应用-2
date: 2018-04-13 09:31:28
categories: js
tags: [js,位运算符]
---

接上篇。

<!-- more -->

### js位运算符的运用

js位运算符的应用主要在于数字，下面是每种运算符相应的一个小应用。

- & 按位与

判断奇偶性：将数字与 1 做按位与计算 即可
```js
0001
0011
____
0001

= 1  
1 代表奇数  0 代表偶数

let n = 3
if(n & 1){   //  if(n%2===1){}
  alert("奇数")
}else{
  alert("偶数")
}
```

- 按位或 |

对浮点数 向下取整

一般会用 Math.floor() 但也可以这样：
```js
var num = Math.floor(1.1); // 1

var num = 1.1 | 0; // 1
```

- 按位非 ~
待补充


- 按位异或 ^

一个数组 里面都是正整数，数字都出现了偶数次（2次，4次...） 唯独有一个数字是 只出现了基数词 找出这个数字？

按平常的做法 ，肯定就对数组进行遍历，对每个数字进行计数。这里用^ 将会非常简洁
```js
var arr = [1,2,3,4,5,1,2,3,4,1,2,3,4,1,2,3,4]; // 只有5 出现了 1次 奇数

arr.reduce((total , _new)=>  total ^ _new )

```

- 有符号左移 <<

算 2 的n次方

```js
function pow(n){
  return 2 << n  
}
```

### 转换颜色值

使用&,>>,|来完成rgb值和16进制颜色值之间的转换

- 16进制颜色值转RGB
```js
function hexToRGB(hex){
    var hex = hex.replace("#","0x"),
        r = hex >> 16,
        g = hex >> 8 & 0xff,
        b = hex & 0xff;
    return "rgb("+r+","+g+","+b+")";
}

```
- RGB转16进制颜色值：
```js
function RGBToHex(rgb){
    var rgbArr = rgb.split(/[^\d]+/),
        color = rgbArr[1]<<16 | rgbArr[2]<<8 | rgbArr[3];
    return "#"+color.toString(16);
}

```


### end

以上的例子可能会遇到 ，但是不会第一时间就想到用 位运算去解决。 在日常开发中，碰到一些复杂的难题，可以试试位运算符，说不定就写出很装逼的代码了。
