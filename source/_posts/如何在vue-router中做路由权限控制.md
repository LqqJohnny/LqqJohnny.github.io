---
title: 如何在vue-router中做路由权限控制
date: 2018-02-25 10:16:32
tags: [vue,权限控制]
---

## vue-router的权限控制

前几天重新对vue-router看了一遍，记录了一下大概的vue-router功能，如下：

<!-- more -->

- 动态路由
- 嵌套路由
- 访问路由
- 路由name
- 重定向
- 别名 alia
- 参数
- 路由守卫
- 过渡动画
- 数据获取
- 滚动行为
- 懒加载

今天来对于权限控制 （路由守卫） 来深入一下

基本上所有对于权限控制的操作都可以在路由守卫中做。

路由守卫 基于 router 的方法 beforeEach

### 全局守卫

下面就是一个简单的全局守卫的写法
```js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

> 如果有多个 全局守卫 ，则会按照声明顺序依次进行验证。

每个守卫接收三个参数：

- to: Route: 即将要进入的目标 路由对象

- from: Route: 当前导航正要离开的路由

- next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。

> 确保要调用 next 方法，否则钩子就不会被 resolved。

**全局后置钩子**
你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 next 函数也不会改变导航本身：
```js

router.afterEach((to, from) => {
  // ...
})
```

### 独享守卫

#### 路由独享

是某一个路由 独有的守卫
```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```
beforeEnter 的参数和 beforeEach 的是一样的。


#### 组件独享

可以在路由组件内直接定义以下路由导航守卫：

- beforeRouteEnter
- beforeRouteUpdate (2.2 新增)
- beforeRouteLeave

```js
const Foo = {
  template: `...`,
  mounted(){},
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
    // 替代方法是 给next传一个回调
    //
    // beforeRouteEnter (to, from, next) {
    //   next(vm => {
    //     // 通过 `vm` 访问组件实例
    //   })
    // }
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

## 例子


```js
router.beforeEach((to, from,next) => {
	let isLogin = true;
	isLogin && limitRouterFunc({
		to,next,
		limitPage:['/login','/register']
	})
})

let limitRouterFunc = args => {
	const { to,next,limitPage } = args;
	if( limitPage.indexOf(to.path)>=0 ){
		next()
	} else {
		Message({
			type: "warning",
			time: 1000,					
			content: `请先登录~！`,
			next:()=>{
				console.log('然后你要登录呢？');
			}
		})
		next({path: limitPage[0]});
	}
}

```

最后在强调，beforeEach 必须要调用next方法 next的参数可见 https://router.vuejs.org/zh-cn/advanced/navigation-guards.html
