---
title: for..in..和 for..of..的区别
date: 2018-04-10 14:49:11
tags: [js,for循环]
---


在ES6中新增了 一个遍历的方法 for .. of ..
之前不是已经有了一个 for .. in .. 了吗 ，for  of 有什么不一样的吗 ？
<!-- more -->

简单说 其实 for of 是对于 for in 的升级

因为for in 能做的 for of 也能做 而且做得更好。

下面来用代码比较一下 ：
```js
// Object 的遍历
let a = {name:"lqq",age:"199"};
for (var key in a) {
  if (a.hasOwnProperty(key)) {
    console.log(a[key]);
  }
}
for (let key of Object.keys(a)){
  if (a.hasOwnProperty(key)) {
    console.log(a[key]);
  }
}
// 两个方法输出的一样


// array 的遍历
let b = [1,2,3,5];
b.name="array";

for (var index in b) {
    console.log(b[index]);
}
// 输出: 1 2 3 4 5 array

for (let value of b){
    console.log(value);
}
// 输出 ： 1 2 3 4 5
```

其实一对比就发现 for in 并不适用于 数组的遍历

for of 就是在数组遍历方面 对于 for in 的补充

> 总结 ： 数组遍历用for of  普通对象遍历用 for in (最好也配合 hasOwnProperty 使用 )

在 不久前google 公布的 js规范里面，就规定循环要是用 for of ，可能就是因为for of 在数组方面确实比 for in 更好点。但是个人觉得不必这么死板，**for of 用于数组， for in 用于对象**就可以了。
