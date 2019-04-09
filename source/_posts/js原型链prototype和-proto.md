---
title: js原型链prototype和__proto__
date: 2018-07-20 13:40:40
categories: js
tags: [js,原型链,prototype]
---

js 的原型链

<!-- more -->

原型链可以让所有的实例都拥有同样的属性或者方法 。

```js
function Person(name){
  this.type = "human";
  this.name = name;
}

Person.prototype.planet = "earth";

var me = new Person("lqq");
console.log(me);
```
在输出的结果中 ，你会发现有三个属性， name ， type ，__proto__ ， 其中的 __proto__  是隐式原型，它指向 Person的 prototype

也就是说 `me.__proto__ === Person.prototype;`

> 函数才有prototype ，所有的对象却都有__proto__ 。

对象 正是通过 __proto__ 才能访问到构造函数的prototype

Person.prototype.__proto__ == Object.prototype

Object.prototype == null 因为Object的上面已经没有构造函数了
