---
title: vuex补漏
date: 2018-07-13 10:16:50
tags: [vue,vuex]
---

之前在项目中用到了vuex，于是便急匆匆的找文档用了，所以也没有认真的从头到尾看一遍，这次有空了看了一遍发现，有很多好用的功能没用到呢，今天在这补下漏。

<!-- more -->

vuex 就不介绍了， 直接开始查缺补漏。

### State
State 是vuex的核心及基础，其他所有的功能，概念都是围绕它展开的， 如何初始化vuex就不说了，直接看文末的参考文档。

值得一提的是，mapState ,它可以一次性引入多个 state属性
```js

import { mapState } from 'vuex'
// ...

computed: mapState({
  count : function(state){ return state.count;},
  // 简化 （箭头函数）
  count: state => state.count,
  // 在简化(传字符串参数)
  count: 'count',
  // 再简化 (需要同名)
  count ,
  // 这个就不能想上一步那样简化
  countAlias: "count",
  // 如果需要对count做一些处理 就必须使用函数了
  countPlusLocalState (state) {
    return state.count + this.localCount
  }
})

```
把state的属性绑定在computed 里面，只要state值变了，computed也会变，就会触发 dom的修改，这也是组件之间传值的基础。

### Getter

Getter就相当于 组件内的计算属性computed。 getter 是 返回一些处理state后的数据，如果不需要处理 ，那直接引用state就行了。

假如， 在一个组件的computed 里面有这么一句：
```js

computed: {
  getMonth () {
    return this.$store.state.date.getMonth()+1;
  }
}
```
如果就这么一个组件 需要获取日期的月份，那这么写ok， 那如果有多个呢，每个组件都要重写一遍，那太low了。
这时候你需要一个getter：
```js
state:{...},
getters: {
    getMonth: state => {
      return state.date.getMonth()+1;
    }
  }


// XXX.vue 使用
this.$store.getters.getMonth  

```
这波操作就很nice ，

Getter 还可以传参数，

```js
getters: {
  // ...
  getMonth: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
// 这个箭头函数的嵌套有点头晕。。。 不吃这个糖了，改一下
getBirth: function(state){
  return  function(name){ // id 就是传的参数
    return state.persons.find(function(person){
      return person.name === name;
    });
  }
}
// 不习惯 箭头函数的我 还是看这个比较舒服 。


this.$store.getters.getBirth("lqq");

```
getter 也有个 mapGetters 函数 ，和mapState用法一致

```js

computed: {
// 使用对象展开运算符将 getter 混入 computed 对象中
  ...mapGetters([
    'doneTodosCount',
    'anotherGetter',
    // ...
  ]),
}

mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount',
})

```
### Mutation

有了Getter获取state属性，接下来就是 修改属性 mutation了

```js
  state: {
    count: 1
  },
  mutations: {
    increment (state,params) {
      state.count+= params.num;
    }
  }


// 调用
this.$store.commit('increment',{num:3})
```
mutations 里面的函数必须是同步的，不能是异步，否则很难调试，如果调用多个异步mutation，你不知道哪个异步函数什么时候调用完成，哪个先完成。

如果有异步的操作，就使用 Actions 。

###  Module

一般的vuex里只到处了一个对象， 当项目比较大，state里面的数据会很多， 你可以进一步模块化，到处多个 module
```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})
this.$store.state.a // -> moduleA 的状态
this.$store.state.b // -> moduleB 的状态

```


> 参考 ： [vuex中文文档](https://vuex.vuejs.org/zh/)
