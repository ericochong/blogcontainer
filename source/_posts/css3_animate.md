---
title: Animate.css官网LOGO颜色渐变动效
tags: CSS3
grammar_cjkRuby: true
---
昨天写完过渡的文章后，我看到了Animate.css官网LOGO大字的动效，瞬间心动，贼好看。所以就自己用蹩脚的css写了个简易版的，只能说功力不到家贼难看，只好在官网上把代码扒下来给大家鉴赏下，涉及到的CSS3属性我也作出写解析。
首先先奉上我蹩脚的简易版：

``` html
<div id="animate_1">
    <h2 class="h2">这就是蹩脚版渐变动画</h2>
  </div>
```
```css
.h2{
      height: 60px;
      color: #f35626;
      -webkit-animation: color 6s infinite linear;
    }
    @-webkit-keyframes color {
      0% {
          color: #f35626;
      }
      50% {
          color: #feab3a;
      }
      100%{
        color: #f35626;
      }
    }
```
这个有个缺陷就是 ，效果只是个颜色的变化，并不是渐变色，只是个渐变效果，而且色域比较窄，没有官网那种炫酷的效果。或许加上JavaScript动态改变色域或者能做到像官网那种较宽色域的渐变，不过依旧不会是渐变色。
下面是官网写的代码：

``` html
<div id="animate_2">
    <h2 class="h2_2">这就是官网炫酷版渐变动画</h2>
 </div>
```
```css
.h2_2{
        height: 60px;
        color: #f35626;
        background: coral;
        background-image: -webkit-linear-gradient(92deg,#f35626,#feab3a);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
        -webkit-animation: hue_2 6s infinite linear;
    }
    @-webkit-keyframes hue_2 {
        from {
            -webkit-filter: hue-rotate(0deg);
        }
        to {
            -webkit-filter: hue-rotate(-360deg);
        }
    }
```

 - **linear-gradient**  这个就是**线性渐变**，也就是做文字上的渐变色用的，用法：
 **background: linear-gradient(direction, color-stop1, color-stop2, ...);**
 **direction**	用角度值指定渐变的方向（或角度）。
 **color-stop1, color-stop2,...**	用于指定渐变的起止颜色。
 
 - **background-clip** 这个是用来确定**背景绘制区域**，默认是**border-box**,用法：
 **background-clip: border-box|padding-box|content-box|text;**
 **border-box**	默认值。背景绘制在边框方框内（剪切成边框方框）。
 **padding-box**	背景绘制在衬距方框内（剪切成衬距方框）。
 **content-box**	背景绘制在内容方框内（剪切成内容方框）。
 **text** 背景描绘在文字上。
 
 - **text-fill-color** 字面上理解就是文字的填充颜色，实现的效果基本上与color一样， 这里要设置为 **transparent**，不然效果实现起来就不好看，用法就和color用法一致。
 - **filter: hue-rotate** 给图像应用色相旋转。"angle"一值设定图像会被调整的色环角度值。值为0deg，则图像无变化。若值未设置，默认值是0deg。该值虽然没有最大值，超过360deg的值相当于又绕一圈。其实我不是很理解这个属性的作用，只能照搬文档。用法：
 **filter: hue-rotate(deg);**

