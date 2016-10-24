title: BFC note
tags:
- css
- 布局
layout: post
categories: CSS
date: 2016-05-05
---

前几天和别人交流的时候，让实现这么一个布局：

![双栏图片布局](http://7fvhwe.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-05-05%20%E4%B8%8B%E5%8D%882.53.39.png)

大概就是上面这个样子。左边是一个图片，右边是文字区域。然后我给的大概实现方式是，左边图片高度宽度固定左浮动，然后右边div框起来左外边距等于图片宽度。

然后这个大兄弟马上说，图片高度宽度不知。我想了想，想起来了overflow:auto可以实现，就这样说了。大兄弟马上问我为啥，我懵逼了。

大兄弟一脸高深的告诉我：知道BFC不？(大兄弟人还是挺好的)

<!-- more -->

不知道可以不，于是就去了解下吧，写这篇笔记，记录下。

## 是个啥

我觉得这句话总结的不错：

> BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。

当元素触发了BFC之后就完全与外部元素隔离了，容器里面的子元素就不会影响外部元素的布局，外部也影响不到内部元素的布局。比如一些margin重合，浮动元素等等的例子。

## 怎么触发

* 根元素
* `float`元素不为none的。
* `position`为`absolute`或则`fixed`
* `display`为`inline-block, table-cell, table-caption, flex, inline-flex`
* `overflow`不为visible

## 作用

### 自适应两栏布局

这里首先要提的一个是：
> 每个元素的margin box的左边与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此。

具体可以看下面的`.left`元素，即使它是浮动元素，它还是与外部元素的border相重合。

在一个就是：

> BFC的区域不会与float box重叠。

大兄弟问的就是上面这条，早晓得了就不会懵逼了。你可以去codepen注释下面的`overflow: hidden`看看效果。

<p data-height="300" data-theme-id="17379" data-slug-hash="mPapdp" data-default-tab="css,result" data-user="hiluluke" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/hiluluke/pen/mPapdp/">mPapdp</a> by hiluluke (<a href="http://codepen.io/hiluluke">@hiluluke</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

### 清除内部浮动

可能你早就知道在父元素设置`overflow:hidden`可以清除浮动，但可能不知道是因为BFC。

BFC有下面一条规则：

> 计算BFC的高度时，浮动元素也参与计算

所以当父元素一旦触发了BFC之后其高度就会计算浮动元素的高度。

<p data-height="300" data-theme-id="17379" data-slug-hash="EKGogy" data-default-tab="css,result" data-user="hiluluke" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/hiluluke/pen/EKGogy/">EKGogy</a> by hiluluke (<a href="http://codepen.io/hiluluke">@hiluluke</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

你可以注释上面outer里面的`overflow: hidden`看看。

### 防止margin重叠

我们知道在一般的w3c box里面如果有margin靠在一起是会发生margin重叠的。而BFC有这么一条规矩：

> Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠

也就是说，不是同一个BFC就不会发生重叠了。

<p data-height="300" data-theme-id="17379" data-slug-hash="YqdYNY" data-default-tab="css,result" data-user="hiluluke" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/hiluluke/pen/YqdYNY/">YqdYNY</a> by hiluluke (<a href="http://codepen.io/hiluluke">@hiluluke</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

可以注释上面的`overflow: hidden`看看。


还有很多有趣的BFC应用，大家可以搜索下。