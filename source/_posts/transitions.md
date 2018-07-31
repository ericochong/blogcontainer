---
title: Vue过渡动画
tags: Vue
grammar_cjkRuby: true
---
## 概述
Vue 在插入、更新或移除 DOM 时，提供了多种方式的应用过渡效果。
可以通过以下方式实现：

 - 在 CSS 过渡和动画中自动应用 **class**
 - 可以配合使用第三方 CSS 动画库，如 **Animate.css**
 - 在过渡钩子函数中使用 **JavaScript** 直接操作 DOM
 - 可以配合使用第三方 JavaScript 动画库，如 **Velocity.js**

## 单元素/组件的过渡（仅仅讲述进入和离开的过渡）
**transition** 组件是Vue中封装好的组件，可以给任何元素和组件添加进入/离开过渡。

 - 条件渲染 (使用 **v-if** )
 - 条件展示 (使用 **v-show** )
 - 动态组件
 - 组件根节点

以上情形可以就可以使用 **transition** 添加进入/离开的过渡。

例如：

``` html
<div id="demo">
  <button v-on:click="show = !show">
    Toggle
  </button>
  <transition name="fade">
  <!-- name属性会替换vue原本的动画属性名前缀 不添加默认前缀为 “v-”-->
    <p v-if="show">hello</p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#demo',
  data: {
    show: true
  }
})
```
```css
//未设置name 会显示为默认前缀 v-enter-active,.v-leave-active,.v-enter,.v-leave-to
.fade-enter-active, .fade-leave-active { 
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
```
以上代码就是 **transition** 的简单使用，在插入或者删除包含在 **transition** 组建中的元素时，Vue会自动查找目标元素是否应用了 **CSS过渡或动画** ，如果是，会在特定的实际添加或者删除**CSS类名**，如果过渡组件提供了 **JavaScript 钩子函数**，也会在恰当时机被调用，如果上述两种都没有，DOM操作就会无动画执行。

## 过渡的类名
![过渡动画类名变化](https://cn.vuejs.org/images/transition.png)
在 **transition** 过渡过程中，会涉及到6个**Class**的切换：

 1. **v-enter** : 定义进入过渡的开始状态。在元素被插入之前生效，在元素被插入之后的下一帧移除。
 2.  **v-enter-active** ：定义进入过渡生效时的状态。在整个进入过渡的阶段中应用，在元素被插入之前生效，在过渡/动画完成之后移除。这个类可以被用来定义进入过渡的过程时间，延迟和曲线函数。
 3.  **v-enter-to** :  定义进入过渡的结束状态。在元素被插入之后下一帧生效 (与此同时 v-enter 被移除)，在过渡/动画完成之后移除。
 4.  **v-leave** :  定义离开过渡的开始状态。在离开过渡被触发时立刻生效，下一帧被移除。
 5.  **v-leave-active** ：定义离开过渡生效时的状态。在整个离开过渡的阶段中应用，在离开过渡被触发时立刻生效，在过渡/动画完成之后移除。这个类可以被用来定义离开过渡的过程时间，延迟和曲线函数。
 6.  **v-leave-to** :  定义离开过渡的结束状态。在离开过渡被触发之后下一帧生效 (与此同时 v-leave 被删除)，在过渡/动画完成之后移除。
除此外 **v-enter-active** 和 **v-leave-active** 可以控制过渡的不同的缓和曲线（通俗理解就是动画速度）。

### CSS过渡 和 CSS动画
#### 过渡:

``` html
<div id="example-1">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition name="slide-fade">
    <p v-if="show">hello</p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#example-1',
  data: {
    show: true
  }
})
```
```css
/* 可以设置不同的进入和离开动画 */
/* 设置持续时间和动画函数 */
.slide-fade-enter-active {
  transition: all .3s ease;
}
.slide-fade-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-fade-enter, .slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
```
常用的过渡都是使用 **CSS 过渡**。

#### 动画：
**CSS 动画** 用法同 **CSS 过渡**，区别是在动画中 v-enter 类名在节点插入 DOM 后不会立即删除，而是在 animationend 事件触发时删除。

``` html
<div id="example-2">
  <button @click="show = !show">Toggle show</button>
  <transition name="bounce">
    <p v-if="show">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris facilisis enim libero, at lacinia diam fermentum id. Pellentesque habitant morbi tristique senectus et netus.</p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#example-2',
  data: {
    show: true
  }
})
```
```css
.bounce-enter-active {
  animation: bounce-in .5s;
}
.bounce-leave-active {
  animation: bounce-in .5s reverse;
}
//定义一个放大缩小的动画
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```
## 自定义过渡的类名(常与第三方CSS动画库联用)

 - **enter-class**
 - **enter-active-class**
 - **enter-to-class**
 - **leave-class**
 - **leave-active-class**
 - **leave-to-class**


优先级高于普通的类名，这对于 Vue 的过渡系统和其他**第三方 CSS 动画库**，如 [Animate.css](https://daneden.github.io/animate.css/) 结合使用十分有用

``` html
<link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">

<div id="example-3">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition
    name="custom-classes-transition"
    enter-active-class="animated tada"
    leave-active-class="animated bounceOutRight"
  >
    <p v-if="show">hello</p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#example-3',
  data: {
    show: true
  }
})
```
### 同时使用过渡和动画
Vue 为了知道过渡的完成，必须设置相应的事件监听器。它可以是 transitionend 或 animationend ，这取决于给元素应用的 CSS 规则。如果你使用其中任何一种，Vue 能自动识别类型并设置监听。
但是，在一些场景中，你需要给同一个元素同时设置两种过渡动效，比如 animation 很快的被触发并完成了，而 transition 效果还没结束。在这种情况中，你就需要使用 type 特性并设置 animation 或 transition 来明确声明你需要 Vue 监听的类型。

``` html
<link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">

<div id="example-4">
  <button @click="show = !show">
    Toggle render
  </button>
  <transition
  	type="transition"
  	name="fade"
    enter-active-class="animated tada fade-enter-active"
    leave-active-class="animated bounceOutRight fade-leave-active"
>
<!-- 在自定义过渡类名中假如过渡类名这样可以同时使用两种过渡动效,"type"用来声明Vue过渡使用哪个的过渡时长-->
    <p v-if="show">hello</p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#example-4',
  data: {
    show: true
  }
})
```
在很多情况下，我们需要统一过渡和动画的总时长，在这种情况下你可以用 **transition** 组件上的 **duration 属性**定制一个显性的过渡持续时间 (以毫秒计)：

``` html
<transition :duration="1000">...</transition>
<!-- 也可以定制进入和移出的持续时间 -->
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

### javascript 钩子

``` html
	<transition
	  v-on:before-enter="beforeEnter"
	  v-on:enter="enter"
	  v-on:after-enter="afterEnter"
	  v-on:enter-cancelled="enterCancelled"

	  v-on:before-leave="beforeLeave"
	  v-on:leave="leave"
	  v-on:after-leave="afterLeave"
	  v-on:leave-cancelled="leaveCancelled"
	>
	</transition>
```
```javascript
// ...
methods: {
  // --------
  // 进入中
  // --------

  beforeEnter: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  enter: function (el, done) {
    // ...
    done()
  },
  afterEnter: function (el) {
    // ...
  },
  enterCancelled: function (el) {
    // ...
  },

  // --------
  // 离开时
  // --------

  beforeLeave: function (el) {
    // ...
  },
  // 此回调函数是可选项的设置
  // 与 CSS 结合时使用
  leave: function (el, done) {
    // ...
    done()
  },
  afterLeave: function (el) {
    // ...
  },
  // leaveCancelled 只用于 v-show 中
  leaveCancelled: function (el) {
    // ...
  }
}
```
这些钩子函数可以结合 CSS transitions/animations 使用，也可以单独使用。
### **注意**:

 1. 当只用 JavaScript 过渡的时候，在 enter 和 leave 中必须使用 done 进行回调。否则，它们将被同步调用，过渡会立即完成。
 2. 推荐对于仅使用 JavaScript 过渡的元素添加 v-bind:css="false"，Vue 会跳过 CSS 的检测。这也可以避免过渡过程中 CSS 的影响。

### javascript 钩子函数的使用 ----- Velocity.js

``` html
<!--Velocity 和 jQuery.animate 的工作方式类似，也是用来实现 JavaScript 动画的一个很棒的选择-->
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

<div id="example-4">
  <button @click="show = !show">
    Toggle
  </button>
  <transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:leave="leave"
    v-bind:css="false"
  >
    <p v-if="show">
      Demo
    </p>
  </transition>
</div>
```
```javascript
new Vue({
  el: '#example-5',
  data: {
    show: false
  },
  methods: {
    beforeEnter: function (el) {
      el.style.opacity = 0
      el.style.transformOrigin = 'left'
    },
    enter: function (el, done) {
      Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
      Velocity(el, { fontSize: '1em' }, { complete: done })
    },
    leave: function (el, done) {
      Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { duration: 600 })
      Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
      Velocity(el, {
        rotateZ: '45deg',
        translateY: '30px',
        translateX: '30px',
        opacity: 0
      }, { complete: done })
    }
  }
})
```
以下是案例完整代码：

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>transitions</title>
  <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
  <style>
    /* 基本过渡 */
    .fade-enter-active, .fade-leave-active {
      transition: opacity 3s;
    }
    .fade-enter, .fade-leave-to {
      opacity: 0;
    }
    /* CSS过渡 */
    /* 可以设置不同的进入和离开动画 */
    /* 设置持续时间和动画函数 */
    .slide-fade-enter-active {
      transition: all .3s ease;
    }
    .slide-fade-leave-active {
      transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
    }
    .slide-fade-enter, .slide-fade-leave-to {
      transform: translateX(10px);
      opacity: 0;
    }
    /* CSS动画 */
    .bounce-enter-active {
      animation: bounce-in .5s;
    }
    .bounce-leave-active {
      animation: bounce-in .5s reverse;
    }
    @keyframes bounce-in {
      0% {
        transform: scale(0);
      }
      50% {
        transform: scale(1.5);
      }
      100% {
        transform: scale(1);
      }
    }
  </style>
</head>
<body>
  <!-- 基本过渡使用 -->
  <div id="demo">
    <button v-on:click="show = !show">
      Toggle
    </button>
    <transition name="fade">
      <p v-if="show">hello</p>
    </transition>
  </div>
  <!-- CSS过渡 -->
  <div id="example-1">
    <button @click="show = !show">
      Toggle render
    </button>
    <transition name="slide-fade">
      <p v-if="show">hello</p>
    </transition>
  </div>
  <!-- CSS动画 -->
  <div id="example-2">
    <button @click="show = !show">Toggle show</button>
    <transition name="bounce">
      <p v-if="show">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris facilisis enim libero, at lacinia diam fermentum id. Pellentesque habitant morbi tristique senectus et netus.</p>
    </transition>
  </div>
  <!-- 自定义过渡的类名 -->
  <link href="https://cdn.jsdelivr.net/npm/animate.css@3.5.1" rel="stylesheet" type="text/css">

  <div id="example-3">
    <button @click="show = !show">
      Toggle render
    </button>
    <transition
      name="custom-classes-transition"
      enter-active-class="animated tada"
      leave-active-class="animated bounceOutRight"
    >
      <p v-if="show">hello</p>
    </transition>
  </div>
  <!-- 同时使用过渡和动画 -->
  <div id="example-4">
    <button @click="show = !show">
      Toggle render
    </button>
    <transition
      type="transition"
      name="fade"
      enter-active-class="animated bounce fade-enter-active"
      leave-active-class="animated bounce fade-leave-active"
    >
  <!-- 在自定义过渡类名中假如过渡类名这样可以同时使用两种过渡动效,"type"用来声明Vue过渡使用哪个的过渡时长-->
      <p v-if="show">hello</p>
    </transition>
  </div>
  <!-- javascript钩子——velocity.js -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

  <div id="example-5">
    <button @click="show = !show">
      Toggle
    </button>
    <transition
      @before-enter="beforeEnter"
      @enter="enter"
      @eave="leave"
      :css="false"
    >
      <p v-if="show">
        Demo
      </p>
    </transition>
  </div>
  <script>
    new Vue({
      el: '#demo',
      data: {
        show: true
      }
    })
    new Vue({
      el: '#example-1',
      data: {
        show: true
      }
    })
    new Vue({
      el: '#example-2',
      data: {
        show: true
      }
    })
    new Vue({
      el: '#example-3',
      data: {
        show: true
      }
    })
    new Vue({
      el: '#example-4',
      data: {
        show: true
      }
    })
    new Vue({
      el: '#example-5',
      data: {
        show: false
      },
      methods: {
        beforeEnter: function (el) {
          el.style.opacity = 0
          el.style.transformOrigin = 'left'
        },
        enter: function (el, done) {
          Velocity(el, { opacity: 1, fontSize: '1.4em' }, { duration: 300 })
          Velocity(el, { fontSize: '1em' }, { complete: done })
        },
        leave: function (el, done) {
          Velocity(el, { translateX: '15px', rotateZ: '50deg' }, { duration: 600 })
          Velocity(el, { rotateZ: '100deg' }, { loop: 2 })
          Velocity(el, {
            rotateZ: '45deg',
            translateY: '30px',
            translateX: '30px',
            opacity: 0
          }, { complete: done })
        }
      }
    })
  </script>
</body>
</html>
```


