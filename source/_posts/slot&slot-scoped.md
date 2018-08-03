---
title: Vue中slot插槽理解使用
tags: Vue
grammar_cjkRuby: true
---
Vue对slot的描述很简单，我一直都看的懵懵懂懂的，知道看到大牛们的描述才有那么点理解。**插槽，也就是slot，是组件的一块HTML模板，这块模板显示不显示、以及怎样显示由父组件来决定。**
从模板种类的角度来分，其实都可以分为**非插槽模板**和**插槽模板**两大类。
**非插槽模板**指的是html模板，指的是‘div、span、ul、table’这些，非插槽模板的显示与隐藏以及怎样显示由插件自身控制；**插槽模板**是slot，它是一个空壳子，因为它显示与隐藏以及最后用什么样的html模板显示由父组件控制。但是插槽显示的位置确由子组件自身决定，slot写在组件template的哪块，父组件传过来的模板将来就显示在哪块。
## 单个插槽
单个插槽就是默认插槽，是不设置name属性的插槽。可以放在组建中的任意位置但是有且仅有一个。
举个例子解释：
父组件：

``` html
<template>
    <div class="father">
        <h3>这里是父组件</h3>
        <child>
            <div class="tmpl">
              <span>菜单1</span>
              <span>菜单2</span>
              <span>菜单3</span>
              <span>菜单4</span>
              <span>菜单5</span>
              <span>菜单6</span>
            </div>
        </child>
    </div>
</template>
```
子组件：

``` html
<template>
    <div class="child">
        <h3>这里是子组件</h3>
        <slot></slot>
    </div>
</template>
```
在这个例子里，因为父组件在<child></child>里面写了html模板，那么子组件的匿名插槽这块模板就是下面这样。也就是说，子组件的匿名插槽被使用了，是被下面这块模板使用了。

``` html
<div class="tmpl">
  <span>菜单1</span>
  <span>菜单2</span>
  <span>菜单3</span>
  <span>菜单4</span>
  <span>菜单5</span>
  <span>菜单6</span>
</div>
```
最终效果就会是这样：

![](./images/736636367-5a69ebacb0d57.png)
## 具名插槽
插槽加了name属性，就变成了具名插槽。具名插槽可以在一个组件中出现N次。出现在不同的位置。
下面的例子，就是一个有两个具名插槽和单个插槽的组件，这三个插槽被父组件用同一套css样式显示了出来。
父组件：

``` html
<template>
  <div class="father">
    <h3>这里是父组件</h3>
    <child>
      <div class="tmpl" slot="up">
        <span>菜单1</span>
        <span>菜单2</span>
        <span>菜单3</span>
        <span>菜单4</span>
        <span>菜单5</span>
        <span>菜单6</span>
      </div>
      <div class="tmpl" slot="down">
        <span>菜单-1</span>
        <span>菜单-2</span>
        <span>菜单-3</span>
        <span>菜单-4</span>
        <span>菜单-5</span>
        <span>菜单-6</span>
      </div>
      <div class="tmpl">
        <span>菜单->1</span>
        <span>菜单->2</span>
        <span>菜单->3</span>
        <span>菜单->4</span>
        <span>菜单->5</span>
        <span>菜单->6</span>
      </div>
    </child>
  </div>
</template>
```
子组件：
<template>
  <div class="child">
    // 具名插槽
    <slot name="up"></slot>
    <h3>这里是子组件</h3>
    // 具名插槽
    <slot name="down"></slot>
    // 匿名插槽
    <slot></slot>
  </div>
</template>
显示效果：

![enter description here](./images/2272418831-5a69ebacc9c41.png)
可以看到，父组件通过html模板上的slot属性关联具名插槽。没有slot属性的html模板默认关联匿名插槽。
## 作用域插槽
实际上，对比前面两种插槽，我们可以叫它带数据的插槽。但是作用域插槽要求，在slot上面绑定数据。

``` html
<slot name="up" :data="data"></slot>
 export default {
    data: function(){
      return {
        data: ['zhangsan','lisi','wanwu','zhaoliu','tianqi','xiaoba']
      }
    },
}
```
我们前面说了，插槽最后显示不显示是看父组件有没有在child下面写模板，像下面那样。

``` html
<child>
   html模板
</child>
```
写了，插槽就总得在浏览器上显示点东西，东西就是html该有的模样，没写，插槽就是空壳子，啥都没有。
OK，我们说有html模板的情况，就是父组件会往子组件插模板的情况，那到底插一套什么样的样式呢，这由父组件的html+css共同决定，但是这套样式里面的内容呢？

正因为作用域插槽绑定了一套数据，父组件可以拿来用。于是，情况就变成了这样：样式父组件说了算，但内容可以显示子组件插槽绑定的。

我们再来对比，作用域插槽和单个插槽和具名插槽的区别，因为单个插槽和具名插槽不绑定数据，所以父组件是提供的模板要既包括样式由包括内容的，上面的例子中，你看到的文字，“菜单1”，“菜单2”都是父组件自己提供的内容；而作用域插槽，父组件只需要提供一套样式（在确实用作用域插槽绑定的数据的前提下）。

下面的例子，你就能看到，父组件提供了三种样式(分别是flex、ul、直接显示)，都没有提供数据，数据使用的都是子组件插槽自己绑定的那个人名数组。
父组件：

``` html
<template>
  <div class="father">
    <h3>这里是父组件</h3>
    <!--第一次使用：用flex展示数据-->
    <child>
      <template slot-scope="user">
        <div class="tmpl">
          <span v-for="item in user.data">{{item}}</span>
        </div>
      </template>

    </child>

    <!--第二次使用：用列表展示数据-->
    <child>
      <template slot-scope="user">
        <ul>
          <li v-for="item in user.data">{{item}}</li>
        </ul>
      </template>

    </child>

    <!--第三次使用：直接显示数据-->
    <child>
      <template slot-scope="user">
       {{user.data}}
      </template>

    </child>

    <!--第四次使用：不使用其提供的数据, 作用域插槽退变成匿名插槽-->
    <child>
      我就是模板
    </child>
  </div>
</template>
```
子组件：

``` html
<template>
  <div class="child">

    <h3>这里是子组件</h3>
    // 作用域插槽
    <slot  :data="data"></slot>
  </div>
</template>

 export default {
    data: function(){
      return {
        data: ['zhangsan','lisi','wanwu','zhaoliu','tianqi','xiaoba']
      }
    }
}
```
显示效果：

![](https://sfault-image.b0.upaiyun.com/344/357/3443579452-5a69ebac86ae9)

原文地址：[深入理解vue中的slot与slot-scope](https://segmentfault.com/a/1190000012996217)