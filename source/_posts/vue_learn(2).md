---
title: Vue基础学习笔记 （二）
tags: vue
grammar_cjkRuby: true
---

# Vue基础学习笔记 （二）— Vue模板语法
## 一、插值
### 文本
Vue中的数据绑定最常见的形式就是使用“Mustache”语法（双大括号）的文本插值：
``` html
<span> Message: {{ msg }} </span>
```
Mustache 标签将会被替代为对应数据对象上 **msg** 属性的值。无论何时，绑定的数据对象上 **msg** 属性发生了改变，插值处的内容都会更新。
使用 **v-once** 指令，可以进行一次性插值，即当数据改变时，插值处也不会因此而更新为新值。
除了双大括号形式可以进行数据绑定，**v-text** 指令也可以进行数据的绑定
``` html
<span v-once> Message: {{ msg }} </span>
```
### 原始HTML
双大括号和 v-text 指令的数据绑定方式都会把数据解析为普通文本，而非HTML代码，为了输出HTML代码，我们需要使用 **v-html** 指令：

``` html
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
···
data：{
	rawHtml:'<span style="color:red">This should be red</span>'
}
```
span中的内容就会被替换成rawHtml中的内容。这时候页面就会显示一段红色This should be red的文本。
### 特性
**v-bind**指令：

``` html
<div v-bind:id="dynamicId"></div>
```
是用于处理Mustache 语法不能作用在HTML特性上的情况，上述代码指在布尔特性下，它们的存在即暗示为 **true** , 在下面的例子中：

``` html
<button v-bind:disabled="isButtonDisabled">Button</button>
```
如果 **isButtonDisabled** 的值是 **null**、**undefined** 或 **false**，则 **disabled** 特性甚至不会被包含在渲染出来的 **button** 元素中。
### 使用JavaScript 表达式
在Vue中所有的数据绑定Vue都提供了完全的 JavaScripot 表达式支持。

``` html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```
这些表达式会在所属 Vue 实例的数据作用域下作为 JavaScript 被解析。有个限制就是，每个绑定都只能包含**单个表达式**，所以下面的例子都**不会生效**

``` html
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}

<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```
不过在正常项目开发中，并不建议在页面上做逻辑判断等的一些操作，页面仅作为数据展示的载体，逻辑处理判断的操作应在 **script** 或者 **js** 文件下进行

## 指令
指令 (Directives) 是带有 **v-** 前缀的特殊特性。指令特性的值预期是单个 JavaScript 表达式 (**v-for** 是例外情况，稍后我们再讨论)。
指令的职责就在于当表达式的值发生改变时，将其产生的连带影响，响应式的作用于 DOM。
常见的指令有：**v-model**、**v-for**、**v-if**、**v-on**、**v-show**等

### v-model
实现双向数据绑定，实时监控数据变化，一般用于表单。

``` html
 <div id="main">
    <input type="text" v-model="content">
    <p> {{content}}</p>
</div>

<script src="./js/vue.js"></script>
<script>
    new Vue({
        el: '#main',
        data: {
            content: ''
        }
    })
</script>
```
在这里，使用**v-model**指令将输入框的值与vue实例中的content进行绑定。此后，二者中的任一值发生变化，另一个值都会跟随变化。
### v-for
用于遍历数组、对象等。

``` html
<div id="main">
    <ul>
        <li v-for="item in arr">  //遍历数组
            {{item}}
        </li>
    </ul>
    <ul>
        <li v-for="item in obj">   //遍历对象
            {{item}}
        </li>
    </ul>
    <ul>      
        <li v-for="(value,key) in obj">   //键值循环，数组也适用，注意key在后面
            {{key}}----{{value}}
        </li>
    </ul>
</div>
<script src="./js/vue.js"></script>
<script>
    new Vue({
        el: '#main',
        data: {
            arr: [1, 2, 3, 4, 5, 6],
            obj: {
                name: 'hedawei',
                age: 30,
                gender: 'man'
            }
        }
    })
</script>
```
在**v-for**后常需要绑定一个**key**值，作为判断独立项的依据，不写也不会影响正常页面显示
### v-on
用于绑定事件，用法：**v-on**:事件="函数"。
下面拿点击事件举例：

``` html
<div id="main">
        <button type="button" v-on:click="showHello()">点击显示</button>
		<p>{{msg}}</p>
</div>

<script src="./js/vue.js"></script>
<script>
    new Vue({
        el: '#main',
        data: {
            msg:'Hello World!'
        },
        methods: {
            showHello() {
                this.msg = 'Hello Vue.js';
            }
        }
    })
</script>
```
**this**指向当前**vue实例**，由此可获取实例的其他属性。想了解更多事件，详情请参考[官网API](https://cn.vuejs.org/v2/api/)
### v-show
用来显示或隐藏元素，**v-show**是通过display实现。当**v-show**的值为**true**时显示，为**false**时隐藏。

``` html
<div id="main">
    <button type="button" v-on:click="change()">隐藏</button>
    <div style="width:100px;height:100px;background:red" v-show="flag"></div>
</div>

<script src="./js/vue.js"></script>
<script>
    new Vue({
        el: '#main',
        data: {
            flag: true
        },
        methods: {
            change() {
                this.flag = !this.flag;
            }
        }
    })
</script>
```
### 修饰符
#### v-on修饰符：
 - **.stop**- 调用 **event.stopPropagation()**。
 - **.preven** - 调用 **event.preventDefault()** 。
 - **.capture** - 添加事件侦听器时使用 **capture** 模式。
 - **.self** - 只当事件是从侦听器绑定的元素本身触发时才触发回调。
 - **.{keyCode | keyAlias}** - 只当事件是从特定键触发时才触发回调。
 - **.native** - 监听组件根元素的原生事件。
 - **.once** - 只触发一次回调。
 - **.left** - (2.2.0) 只当点击鼠标左键时触发。
 - **.right** - (2.2.0) 只当点击鼠标右键时触发。
 - **.middle**  - (2.2.0) 只当点击鼠标中键时触发。
 - **.passive** - (2.3.0) 以 **{ passive: true }** 模式添加侦听器
 参考：
 - [事件处理器](https://cn.vuejs.org/v2/guide/events.html)
 - [组件 - 自定义时间](https://cn.vuejs.org/v2/guide/components.html#%E9%80%9A%E8%BF%87%E4%BA%8B%E4%BB%B6%E5%90%91%E7%88%B6%E7%BA%A7%E7%BB%84%E4%BB%B6%E5%8F%91%E9%80%81%E6%B6%88%E6%81%AF)

#### v-bind修饰符
 - **.prop** - 被用于绑定 DOM 属性 (property)。
 - **.camel** - (2.1.0+) 将 kebab-case 特性名转换为 camelCase. (从 2.1.0 开始支持)
 - **.sync** (2.3.0+) 语法糖，会扩展成一个更新父组件绑定值的 v-on 侦听器。
参考：
 - [Class 与 Style 绑定](https://cn.vuejs.org/v2/guide/class-and-style.html)
 - [组件 - Props](https://cn.vuejs.org/v2/guide/components.html#logo)
 - [组件- 。sync 修饰符](https://cn.vuejs.org/v2/guide/components-custom-events.html#sync-%E4%BF%AE%E9%A5%B0%E7%AC%A6)

#### v-model修饰符
 - **.lazy** - 取代 input 监听 change 事件
 - **.number** - 输入字符串转为数字
 - **.trim** - 输入首尾空格过滤
参考：
 - [表单控件绑定](https://cn.vuejs.org/v2/guide/forms.html)
 - [组件 - 在输入组件上使用自定义事件](https://cn.vuejs.org/v2/guide/components-custom-events.html#%E5%B0%86%E5%8E%9F%E7%94%9F%E4%BA%8B%E4%BB%B6%E7%BB%91%E5%AE%9A%E5%88%B0%E7%BB%84%E4%BB%B6)

### 缩写
**v-** 前缀作为一种视觉提示，用来识别模板中 Vue 特定的特性。当你在使用 Vue.js 为现有标签添加动态行为 (dynamic behavior) 时，**v-** 前缀很有帮助，然而，对于一些频繁用到的指令来说，就会感到使用繁琐。同时，在构建由 Vue.js 管理所有模板的单页面应用程序 ****(SPA - single page application****) 时，**v-** 前缀也变得没那么重要了。因此，Vue.js 为 **v-bind** 和 **v-on** 这两个最常用的指令，提供了特定简写：
#### **v-bind** 缩写 " : "。
#### **v-on** 缩写 " @ "。

正因为这些缩写的存在，为实际项目书写提供了很大的便利。慢慢你就能体会到他们的魅力。