title: 12个鲜为人知的css现象（续集）
tags:
- css
- 翻译
layout: post
categories: CSS
date: 2016-01-10
---


> 本文翻译自 [12 little known css facts(the-sequel)](http://www.sitepoint.com/12-little-known-css-facts-the-sequel/?utm_source=html5weekly&utm_medium=email)
原作者是[Natalia Balska](http://scncf-lab.com/)



原作者在一年前也发表过一篇[12个鲜为人知的css现象](http://www.sitepoint.com/12-little-known-css-facts/)，然后这篇文章现在也还很受欢迎，有时间的同学可以看看。原作者又收集了更多的鲜为人知的css性质，也就是这篇文章的来源。

### 1、`border-radius` 有“／”的语法

首先，下面这个你信或则不信它都是一条`border-radius`的正确的代码：
```css
.box {
  border-radius: 35px 25px 30px 20px / 35px 25px 15px 30px;
}
```
如果你没见过，可能会有些困惑，下面是一些介绍：
> 如果给定的值中有“／”，则在“／”前面的值是指定水平方向的半径（radius），在“／”后面的就是指定垂直方向的半径（radius）。如果没有“／”，就是同时指定水平和垂直方向的半径（radius）。

<!-- more -->

![border-radius 图解](http://7fvhwe.com1.z0.glb.clouddn.com/border.png)

上面的图片表示：`border-top-left-radius: 55pt 25pt`。

而带有“／”的border-radius能让你创建非对称的弯曲边角。原作者有有一篇文章详细讲了，或则你可以读下面的张鑫旭的这篇。另外，你也可以去[MDN-boreder-radius](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Background_and_Borders/Border-radius_generator),学习下。

`border-radius`值的设定在张鑫旭的博客[秋月何时了，CSS3 border-radius知多少？](http://www.zhangxinxu.com/wordpress/2015/11/css3-border-radius-tips/)上介绍的更为详细，有兴趣的同学可以去看看。


### 2、`font-weight` 的值可以设置为相对值

你应该在设置`font-weight`值时用过`normal`或则`bold`，更甚你可能用过`100`,`200`到`900`的百位数。

有两个值经常被忘记，他们是`bolder`和`lighter`。在他们的描述中，其是相对于其父元素的`font-weight`更粗，或则更细。

在基于百位数的值中，`bold`相当于`700`而`normal`相当于`400`。因此如果存在300的值，当父对象是400时，lighter就等于300。而当没有更小的font-weight值时（比如400是最小的），那么lighter最小只能是400。 存不存在100、200、到900的font-weight是由使用的字体相关的。

下面是一个codepen的demo

<p data-height="268" data-theme-id="17379" data-slug-hash="obWOWq" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/obWOWq/'>Using bolder/lighter Keywords with font-weight</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

在上面的例子中使用的字体是Exo2，其有18种不同的样式，所以能够展示所有基于百位的font-weight。上面的css文件中注释中在中庸bolder和lighter展示了对应的值。

你会发现，上面的例子只会有100，400，700，900。并不会有200，300，500，600，800。

这是字体自己定义的，Exo2这个字体就定义了100，400，700，900这几种font-weight，所以再取bolder和lighter时会在这几个值种选（就像这个[栗子](https://www.google.com/fonts/specimen/Open+Sans)）。

### 3、`outine-offset`属性

outine-offset 比较出名的是其能帮助debuging(因为它不影响页面布局)。就像它名字一样，他是定义元素的outline和元素的距离的。看下面一个demo：

<p data-height="268" data-theme-id="17379" data-slug-hash="rxmbzy" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/rxmbzy/'>The outline-offset property</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

对于outline-offset的不利就是，其能适用于所有浏览器除了ie11及以前。在移动端能够应用下。

### 4、`table-layout`属性（个人感觉最有用的属性）

你可能知道`display:table`（最简单粗暴的实现垂直居中的方式）。但是这个不是我们说的，我们说的是table-layout属性而不是display属性。

`table-layout`属性不是一个好描述的属性，我们先简单看个解释然后看个栗子。

> table的水平宽度是取决于内容的，这个属性就是改变使其取决于一行的border、spacing等内容。

就算我转译了一下意思，应该还是不好理解。看下面的一个demo吧：

<p data-height="268" data-theme-id="17379" data-slug-hash="yebrPJ" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/yebrPJ/'>Using the table-layout property</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

上面的demo种加入了`table-layout:fixed`,单击下面的按钮可以来回切换属性。

默认的是`table-layout:auto`。但是当有变化长度的内容时，你的排版会很乱（我之前是定的width：n％,当出现33.3% 时候就会有一列大一像素），这个时候使用fixed就很好。

#### 5、`vertical-align`在table cells和其他属性表现不一致

如果你感web编程在2000年代中期或则更早，vertical-align属性是html4标准的[valign属性](http://www.w3.org/TR/html401/struct/tables.html#adef-valign)。但是现在HTML5中就是过时的不合格的属性。

其实它并不只是在table中起作用，它在其他元素的定位上也很有意义。

那么它在其他元素和table cells中的表现有哪些不同？

当在其他元素中时：
* 它只在`inline`和`inline-block`元素中起作用
* 它对元素内容没影响，只对元素相对于其他`inline`和`inline-block`元素的位置有影响。
* 它能够被相邻元素的text/font属性影响，比如`line-height`属性。

下面是一个栗子：

<p data-height="268" data-theme-id="17379" data-slug-hash="yebrKa" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/yebrKa/'>Using the vertical-align property on inline elements</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

当在table cells中时，vertical-align表现就非常不同了。当你给多个table cells设置值时，他们会被不同的基线影响。

<p data-height="268" data-theme-id="17379" data-slug-hash="mVmgKo" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/mVmgKo/'>Using vertical-align on table cells</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

### 6、::first-letter伪元素比你想象中更智能

感觉再智能，我们也用的不多。主要是英文有种格式会用到，咱略过。如果你感兴趣可以去看原文。

### 7、你可以使用非法字符作为类名列表里

这个观点在[Using slashes within the HTML class attribute](http://beneverard.co.uk/blog/using-slashes-within-the-html-class-attribute/)中提到，然后我认为值得推荐。

ben的观点是使用“／”去将类名分组，从而使得类名更容易阅读。他在文章中指出保留的“／”符号虽然是一个非法字符，但是浏览器不会在使用它是阻断，而是忽略它。

你可能看到过如下的代码：

```html
<div class="col col-4 col-8 c-list bx bx--rounded bx--transparent">
```

当使用“／”时，它就变成：
```html
<div class="col col-4 col-8 / c-list / bx bx--rounded bx--transparent">
```

你能够使用任何字符（违规或则不违规）去产生相同的效果：

```html
<div class="col col-4 col-8 ** c-list ** bx bx--rounded bx--transparent">

<div class="col col-4 col-8 || c-list || bx bx--rounded bx--transparent">

<div class="col col-4 col-8 && c-list && bx bx--rounded bx--transparent">

```

当然，你不可以在css里面用上面的违规字符。比如下面这个就是违法的并且不能应用相应的样式。
```css
./ {
  color: blue;
}
```

当你使用这些特殊字符在css中时需要转义。可以用这个[工具](https://mothereff.in/css-escapes)转义。

比如上面的就会被转义为：

```css
.\/ {
  color: blue;
}
```
其他编码的字符也可以使用，通过转义工具转义就行了。比如♥ ★。。。（感觉可以自己玩玩，生产环境这么弄，要被其他队友骂死）

### 8， Animation iterations 能够是分数

我们应该已经知道`animation-iteration-count`是用来说明keyframe 动画的播放次数的。比如：

```css
.example {
  animation-iteration-count: 3;
}
```

上面的整数值表示动画将会播放3次，但是你可能不知道这里可以使用小数（分数）值。

```css
.example {
  animation-iteration-count: .5;
}
```

上面这个动画将会在半路就停止，也就是在第一次播放动画中间时间停止。下面是一个栗子，点击按钮，观看。

<p data-height="268" data-theme-id="17379" data-slug-hash="wMdbKG" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/wMdbKG/'>Using Fractional Iteration Count with Linear Timing Function</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>


有趣的是，iteration duration是根据时间来算的。也就是说，如果你动画是移动100px，那么如上面值为0.5时，将可能不在50px停，这个和`animation-timing-function`有关。

比如下面的栗子，`animation-timing-function`设置的是`ease`：

<p data-height="268" data-theme-id="17379" data-slug-hash="pgPmgw" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/pgPmgw/'>pgPmgw</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

具体就不解释了。上面栗子很好懂。


### 9， 动画规则缩写会被动画名字给阻断

这个很好理解，大概说下。就是如下
```css
.example {
  animation: reverse 2s 1s;
}
```

在指定动画时用了将多个值都放在animation时，其中reverse又是`animation-direction`的关键字。所以这里在实际中将不会运行。

所以实际中，需要注意动画命名不要和关键字重复。

### 10，你能够选择一定范围的元素

不知道是谁最先使用，第一次看见是在这个[demo](http://bittersmann.de/samples/08-15)。假设有一个20个元素的列表，你想要选择其中的7到14号，你可以像下面这样只是用一个选择器选择它：

```css
ol li:nth-child(n+7):nth-child(-n+14) {
  background: lightpink;
}
```

<p data-height="268" data-theme-id="17379" data-slug-hash="obWRLV" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/obWRLV/'>Selecting Ranges of Elements with CSS</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

感觉用到不会太多。有兴趣的看看原文吧。原文还指出了一个在safari上的bug。

### 11， 伪元素能够应用于一些空节点(没有结尾</>的节点)

有时候要给一些节点加伪元素，但是这些节点会被替换是不能添加伪元素的。这个时候就能使用哪些没有结尾的空节点，因为这些节点将不会被替换，比如`hr`节点。

<p data-height="268" data-theme-id="17379" data-slug-hash="MKmdJr" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/MKmdJr/'>Pseudo-elements on a Horizontal Rule (<hr> element)</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

你同样能在`meta`和`link`节点上加伪元素，只要你设置他们`display:block`；


<p data-height="268" data-theme-id="17379" data-slug-hash="dGWENE" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/dGWENE/'>Adding pseudo-elements to meta tags and link (stylesheet) elements</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>


### 12, 一些节点属性是大小写敏感的

```html
<div class="box"></div>
<input type="email">
```

你可以使用属性选择来选取上面的节点：

```css
div[class="box"] {
  color: blue;
}

input[type="email"] {
  border: solid 1px red;
}
```

上面很正常，但是如果像下面这样：

```css
div[class="BOX"] {
  color: blue;
}

input[type="EMAIL"] {
  border: solid 1px red;
}
```
又会怎样呢？

上面的.box不会表现出blue的样式，因为class是大小写敏感的，而input会表现出相应的样式，因为其不名字不大小写敏感。



就这。


> 如果你要转载。记得说是从http://hiluluke.cn转载的。
