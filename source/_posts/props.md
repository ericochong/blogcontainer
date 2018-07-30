---
title: 非父子组件间的数据传递
tags: Vue
grammar_cjkRuby: true
---
在日常项目中除了简单的父子组件间的数据传递，很多场景下我们还需要在不同组件之间（非父子）进行数据的传递。Vue官方推荐使用一个**Vue实例作为中央事件总线**。
Vue内部有一个事件机制，$on方法用来监听一个事件，$emit用来触发一个事件。

``` html
<div id="root">
	<child content = "Hello"></child>
	<child contetn = "World"></child>
</div>
<script>
//新建一个Vue实例作为中央事件总线新建在Vue的prototype上方便全局使用
	Vue.prototype.bus = new Vue();
	Vue.component('child',{
		props: {
			content: String
		},
		data(){
			return {
				str = this.content
			}
		},
		template: '<div @click="handleClick">{{str}}</div>',
		methods: {
			handleClick:  function (){
				this.bus.$emit('change',this.str)//触发事件
			}
		}，
		mounted() {
			var this_ = this;
			//监听事件
			this.bus.$on('change',function(msg){
				this_.str = msg;
			})
		}
	})\
	var vm = new Vue({
		el: '#root'
	})
</script>
```


