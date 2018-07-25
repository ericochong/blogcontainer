---
title: js判断一个元素是不是另一个元素的子元素
tags: JS,DOM
grammar_cjkRuby: true
---


   在做一些效果时，很经常会用到 ==这个元素是否在指定元素内 #03a9f4==，特别是在一些鼠标相关事件的应用中，例如，在做弹窗的时候，最常遇到的需求就是，点击弹窗外的部分弹窗需要收回。实现方式多种多想，下面是我学习到的一种方式：

``` javascript
function isInThis(target,parent){
	//首先要做空元素判断和是否为body
	whild ( target != undifinde &&obj != null && obj.tagName.toUpperCase() != 'BODY'){
		//判断当前点击对象是否是所指定的元素
		if ( target == parent ) {
			//判断成功返回true
			return true;
		}
		//上述条件不满足将target重新赋值为其上一级
		target = target.parentNode;
	}
	//不满足所有返回false
	return false;
}
```
上述的方法是我说了解到的比较简单的而且没有任何兼容性问题的方法，下面描述第二种方法，就是js的contains方法，这个方法通常会用来判断一个字符串是否包含另外一个字符串，也可以用来判断元素间的包含关系。
	   
	   代码：
``` javascript
function isContains(target,parent){
	//containe方法会直接返回true或者false,用法为父元素.contains(子元素)
	return parent.contains(target);
}
```
FireFox除了支持Contains方法外，还支持compareDocumentPosition()这个方法，这是W3C制定的方法，标准浏览器都支持，但是由于实用性比较差，故而没什么人用，它返回的并不是简单的true和false，而是一串奇怪的数值，是通过下表的值计算出来的。

| Bits   | Number | Meaning                              |
| ------ | ------ | ------------------------------------ |
| 000000 | 0      | 元素一致                             |
| 000001 | 1      | 节点在不同的文档（或者一个在文档外） |
| 000010 | 2      | 节点B在节点A之前                     |
| 000100 | 4      | 节点A在节点B之前                     |
| 001000 | 8      | 节点B包含节点A                       |
| 010000 | 16     | 节点A包含节点B                       |
| 100000 | 32     | 浏览器的私有使用                     |

 对于我这种数学无能的人来说，计算永远都是痛。、
下面我只能贴上我抄来的代码，其实我也不是很理解，仅作为学习参考。
### HTML:
``` html
<div id="parent" style=" padding:10px; border:1px solid #DDD; background:#678789">
  <p>这里是父元素</p>
  <p>
    <strong id="child" style=" padding:10px; border:1px solid #DDD; background:#FE7878">这个是子元素</strong>
  </p>
</div>
```

### JavaScript:

``` javascript
 window.onload = function(){
    var A = document.getElementById('parent'),
    B = document.getElementById('child');
    alert(A.compareDocumentPosition(B));//B与A不相连，B在A的后面，B被A包含 4+16 = 20
    alert(B.compareDocumentPosition(A));//A与B不相连，A在B的前面，A包含B 2+8 = 10
}
```
上述就是我学习到的对元素位置的判断，希望能帮助到大家！