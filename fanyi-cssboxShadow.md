title: CSS盒阴影效果
subtitle: 日常翻译三
tags: 
- HTML5
- css
- 翻译
layout: post
categories: HTML5
date: 2015-08-15
---

在这个教程中，我们将只使css用创建几种不同的盒阴影效果。下面这个图片是在photoshop下创建的，并具有不同的盒阴影。过去要创建这么棒的效果只能使用photoshop，但是现在我们可以使用CSS3来实现了。
<!--more-->

![demo](http://7fvhwe.com1.z0.glb.clouddn.com/Web-Shadows-Package.png)
你可以先看看[Demo页面](http://www.paulund.co.uk/playground/demo/css_box_shadow/)。来了解下我们将要创建的东西。

## CSS盒阴影

我们将开始使用CSS盒阴影属性，这个是我最喜爱的CSS属性之一。你将在本教程看到我们使用它是多么的容易。

和阴影允许我们通过指定颜色、大小、模糊半径来轻松的在一个盒子上创建复合阴影。

盒阴影属性有2-6个参数可填。必须的参数是水平位移和纵向位移，两个可选的参数是展开的距离和颜色。

```css
box-shadow: inset horizontal vertical blur spread colour;
```


例子：

```css
box-shadow: 10px 10px;
box-shadow: 10px 10px 5px #888;
box-shadow: inset 2px 2px 2px 2px black;
box-shadow: 10px 10px #888, -10px -10px #f4f4f4, 0px 0px 5px 5px #cc6600;
```

### 支持的浏览器

所有主流浏览器都支持盒阴影。

* ie9或则更高
* 火狐3.5或则更高
* chrome1或则更高
* safari 3或则更高
* Opera10.5或则更高

很多新的css3特性都需要加上浏览器前缀。

比如火狐的需要加上-moz-,而chrome和safari需要加上-webkit-。

box-shadow特性也需要加上前缀。对于火狐3.5及以下你需要加上前缀就像：-moz-box-shaodw，但是对于火狐4.0或则更高版本，你就不需要加上前缀了。
chrome和safari需要加上对应的前缀，而opera就不需要前缀了。

## CSS盒阴影特效1

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects1-590x206.png)

第一个特效是去创建一个阴影在盒子的下方，使得其像是浮雕一样。

**HTML**：

```html
<div class="box effect1">
	<h3>Effect 1</h3>
</div>
```

**CSS**:

```css
.box h3{
	text-align:center;
	position:relative;
	top:80px;
}
.box {
	width:70%;
	height:200px;
	background:#FFF;
	margin:40px auto;
}


.effect1{
	-webkit-box-shadow: 0 10px 6px -6px #777;
	   -moz-box-shadow: 0 10px 6px -6px #777;
	        box-shadow: 0 10px 6px -6px #777;
}
```

## CSS盒阴影特效2

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects2-590x206.png)

这个效果将会在盒子底部的两边加上一个阴影使得盒子看起来像被举起来一样。这个特效同时使用:before和:after去创建伪元素形成这个corner。

**HTML**:

```html
<div class="box effect2">
	<h3>Effect 2</h3>
</div>
```
**CSS**:

```css
.box h3{
	text-align:center;
	position:relative;
	top:80px;
}
.box {
	width:70%;
	height:200px;
	background:#FFF;
	margin:40px auto;
}


.effect2
{
  position: relative;
}
.effect2:before, .effect2:after
{
  z-index: -1;
  position: absolute;
  content: "";
  bottom: 15px;
  left: 10px;
  width: 50%;
  top: 80%;
  max-width:300px;
  background: #777;
  -webkit-box-shadow: 0 15px 10px #777;
  -moz-box-shadow: 0 15px 10px #777;
  box-shadow: 0 15px 10px #777;
  -webkit-transform: rotate(-3deg);
  -moz-transform: rotate(-3deg);
  -o-transform: rotate(-3deg);
  -ms-transform: rotate(-3deg);
  transform: rotate(-3deg);
}
.effect2:after
{
  -webkit-transform: rotate(3deg);
  -moz-transform: rotate(3deg);
  -o-transform: rotate(3deg);
  -ms-transform: rotate(3deg);
  transform: rotate(3deg);
  right: 10px;
  left: auto;
}
```

## CSS盒阴影特效3

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects3-590x205.png)

这个只要前面的特效的一半即可。


相应代码就是删掉上面的effect3：after


## CSS盒阴影特效4

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects4-590x205.png)

删掉右边的部分，原博主有点有趣。。

## CSS盒阴影特效5

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects5-590x223.png)

这个扩大了特效2的角度。

**HTML**:

```html
<div class="box effect5">
	<h3>Effect 5</h3>
</div>
```

**CSS**:

```css
.box h3{
	text-align:center;
	position:relative;
	top:80px;
}
.box {
	width:70%;
	height:200px;
	background:#FFF;
	margin:40px auto;
}

/*==================================================
 * Effect 5
 * ===============================================*/
.effect5
{
  position: relative;
}
.effect5:before, .effect5:after
{
  z-index: -1;
  position: absolute;
  content: "";
  bottom: 25px;
  left: 10px;
  width: 50%;
  top: 80%;
  max-width:300px;
  background: #777;
  -webkit-box-shadow: 0 35px 20px #777;
  -moz-box-shadow: 0 35px 20px #777;
  box-shadow: 0 35px 20px #777;
  -webkit-transform: rotate(-8deg);
  -moz-transform: rotate(-8deg);
  -o-transform: rotate(-8deg);
  -ms-transform: rotate(-8deg);
  transform: rotate(-8deg);
}
.effect5:after
{
  -webkit-transform: rotate(8deg);
  -moz-transform: rotate(8deg);
  -o-transform: rotate(8deg);
  -ms-transform: rotate(8deg);
  transform: rotate(8deg);
  right: 10px;
  left: auto;
}
```

## CSS盒阴影特效6

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects6-590x192.png)

这个特效将会创建一个弧形的阴影。

**HTML**

```html
<div class="box effect6">
	<h3>Effect 6</h3>
</div>
```

**CSS**

```css
.box h3{
	text-align:center;
	position:relative;
	top:80px;
}
.box {
	width:70%;
	height:200px;
	background:#FFF;
	margin:40px auto;
}

/*==================================================
 * Effect 6
 * ===============================================*/
.effect6
{
  	position:relative;       
    -webkit-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
       -moz-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
            box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
}
.effect6:before, .effect6:after
{
	content:"";
    position:absolute; 
    z-index:-1;
    -webkit-box-shadow:0 0 20px rgba(0,0,0,0.8);
    -moz-box-shadow:0 0 20px rgba(0,0,0,0.8);
    box-shadow:0 0 20px rgba(0,0,0,0.8);
    top:50%;
    bottom:0;
    left:10px;
    right:10px;
    -moz-border-radius:100px / 10px;
    border-radius:100px / 10px;
}
```

## CSS盒阴影特效7

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects7-590x202.png)

这个特效利用特效6的特效，并将之加在了盒子的上方。



**HTML**

```html
<div class="box effect7">
	<h3>Effect 7</h3>
</div>

```

**CSS**

```css
.box h3{
	text-align:center;
	position:relative;
	top:80px;
}
.box {
	width:70%;
	height:200px;
	background:#FFF;
	margin:40px auto;
}

/*==================================================
 * Effect 7
 * ===============================================*/
.effect7
{
  	position:relative;       
    -webkit-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
       -moz-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
            box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
}
.effect7:before, .effect7:after
{
	content:"";
    position:absolute; 
    z-index:-1;
    -webkit-box-shadow:0 0 20px rgba(0,0,0,0.8);
    -moz-box-shadow:0 0 20px rgba(0,0,0,0.8);
    box-shadow:0 0 20px rgba(0,0,0,0.8);
    top:0;
    bottom:0;
    left:10px;
    right:10px;
    -moz-border-radius:100px / 10px;
    border-radius:100px / 10px;
} 
.effect7:after
{
	right:10px; 
    left:auto;
    -webkit-transform:skew(8deg) rotate(3deg); 
       -moz-transform:skew(8deg) rotate(3deg);     
        -ms-transform:skew(8deg) rotate(3deg);     
         -o-transform:skew(8deg) rotate(3deg); 
            transform:skew(8deg) rotate(3deg);
}
```


##  CSS盒阴影特效8

![](http://7fvhwe.com1.z0.glb.clouddn.com/CSS-Box-Shadow-Effects8-590x199.png)

第8个特效将在每一条边都加入一个圆形的阴影。

**HTML**

```html
<div class="box effect8">
	<h3>Effect 8</h3>
</div>

```

**CSS**

```css
.box h3{
	text-align:center;
	position:relative;
	top:80px;
}
.box {
	width:70%;
	height:200px;
	background:#FFF;
	margin:40px auto;
}

/*==================================================
 * Effect 8
 * ===============================================*/
.effect8
{
  	position:relative;       
    -webkit-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
       -moz-box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
            box-shadow:0 1px 4px rgba(0, 0, 0, 0.3), 0 0 40px rgba(0, 0, 0, 0.1) inset;
}
.effect8:before, .effect8:after
{
	content:"";
    position:absolute; 
    z-index:-1;
    -webkit-box-shadow:0 0 20px rgba(0,0,0,0.8);
    -moz-box-shadow:0 0 20px rgba(0,0,0,0.8);
    box-shadow:0 0 20px rgba(0,0,0,0.8);
    top:10px;
    bottom:10px;
    left:0;
    right:0;
    -moz-border-radius:100px / 10px;
    border-radius:100px / 10px;
} 
.effect8:after
{
	right:10px; 
    left:auto;
    -webkit-transform:skew(8deg) rotate(3deg); 
       -moz-transform:skew(8deg) rotate(3deg);     
        -ms-transform:skew(8deg) rotate(3deg);     
         -o-transform:skew(8deg) rotate(3deg); 
            transform:skew(8deg) rotate(3deg);
}
```


最后原博主还给了个，[CSS盒阴影生成器](https://coveloping.com/tools/css-box-shadow-generator)。





** 本文翻译自[CSS Box Shadows Effects](http://www.paulund.co.uk/creating-different-css3-box-shadows-effects),如果有问题请在评论中指出,谢谢 **
