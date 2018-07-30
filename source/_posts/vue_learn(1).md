---
title: Vue基础学习笔记 （一）
tags: Vue
grammar_cjkRuby: true
---


# 一、介绍
Vue是一套用于构建用户界面的渐进式框架，说白了就是个很牛X的前端框架，谁用谁知道。
Vue的特点：

 - 是一个构建用户界面的框架
 - 是一个轻量级的MVVM框架，类似于Angular、react这样的
 - 是数据驱动和组件化的前端开发方式
 - 丰富简单的API实现响应式的数据绑定和组合的视图组件
 - 容易上手
第一次接触的同学建议先去通读  [官方文档](https://cn.vuejs.org/) 。

# 二、第一个实例

``` html
<div id="app">
  {{ message }}
</div>
<script src="./js/vue.js"></script>   //引入vue文件
<script>
	var app = new Vue({
		el:"#app",
		data:{
			messae: "Hello Vue!"
		}
	});
</script>
```
以上就是最简单的Vue应用，看起来这跟渲染一个字符串模板非常类似，但是 Vue 在背后做了大量工作。现在数据和 DOM 已经被建立了关联，所有东西都是响应式的。我们要怎么确认呢？打开你的浏览器的 JavaScript 控制台 (就在这个页面打开)，并修改 app.message 的值，你将看到上例相应地更新。
每个Vue应用都是通过 **Vue**  函数创建一个新的 **Vue 实例** 开始的：

``` js
var app = new Vue({ 
	//选项
});
```
**el** 传入的是Vue实例的作用域，所有Vue的操作都需要在该作用域下才会起作用。
**data #dd146a**  对象包含了一个Vue实例所有的属性。当属性的值被改变是，视图就会产生 “响应”，即匹配更新为新的值。

### 实例生命周期钩子
每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做生命周期钩子的函数，这给了用户在不同阶段添加自己的代码的机会。
比如 created 钩子可以用来在一个实例被创建之后执行代码：

``` js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```
### ***注意：***

 > 不要在选项属性或回调上使用箭头函数，
 > 比如 created: () => console.log(this.a) 或 vm.$watch('a', newValue => this.myMethod())。
 > 因为箭头函数是和父级上下文绑定在一起的，this 不会是如你所预期的 Vue 实例，经常导致 
 > Uncaught TypeError: Cannot read property of undefined 或
 > Uncaught TypeError: this.myMethod is not a function 之类的错误。
 
 ### 生命周期图示
 ![enter description here](https://cn.vuejs.org/images/lifecycle.png)
 图中的方法并不需要立马了解清楚，在不断的学习使用后自然而然能掌握了。
 
 
 


