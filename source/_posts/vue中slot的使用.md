---
title: vue中slot的使用
date: 2019-04-09 11:58:28
categories: vue
tags: [vue,slot]
---
参考： https://segmentfault.com/a/1190000013277423

### 分类 

- 匿名插槽
- 具名插槽
- 作用域插槽（带数据的插槽）

<!-- more -->

### 匿名插槽

```html
<!--父组件-->
<template>
    <div class="father">
        <h3>这里是父组件</h3>
        <child>
            <div class="tmpl">
              <span>菜单1</span>
            </div>
        </child>
    </div>
</template>


<!--子组件：-->
<template>
    <div class="child">
        <h3>这里是子组件</h3>
        <slot></slot>
    </div>
</template>

```
子组件中的 `<slot></slot>` 就是一个插槽 ， 等待着被插入（/奸笑）
他等待着被父组件中的东西插入，具体的也就是`<child></child>`中间的这段代码：
```html
<div class="tmpl">
  <span>菜单1</span>
</div>
```
匿名插槽 就默认的选择 `<child></child>` 内部代码插入。

### 具名插槽

和匿名插槽类似， 不同的就是 在 `<child></child>` 中可以设置 插槽的 name ，

```html
<!--父组件：-->
<template>
  <div class="father">
    <h3>这里是父组件</h3>
    <child>
        <!--声明 一个叫做 up 的插槽-->
      <div class="tmpl" slot="up">
        <span>上上上</span>
      </div>
      <!--声明 一个叫做 down 的插槽-->
      <div class="tmpl" slot="down">
        <span>下下下</span>
      </div>
    </child>
  </div>
</template>
<!--在父组件中只是声明一个模板 ， 用来插入子组件 ，即这里的 up 和 down    
  slot-->


<!--具名插槽可以定制化 多个插槽显示顺序-->
<!--子组件-->
<template>
  <div class="child">
    <slot name="down"></slot>
    <h3>这里是子组件</h3>
    <slot name="up"></slot>
  </div>
</template>

```
