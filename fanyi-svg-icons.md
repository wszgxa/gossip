title: 牛逼的SVG图标
subtitle: 日常翻译一
tags: 
- HTML5
- SVG
- 翻译
layout: post
categories: HTML5
date: 2015-07-28
---

尽管SVG的普及，并在我们的浏览器当中得到很好的支持，我们还是缺少好的构造icons的方法。是时候填补这个缝隙开始将SVG作为icons应用于我们的项目当中。

![](http://7fvhwe.com1.z0.glb.clouddn.com/SVGIcons.png)

很多伟大的方法和原则的出现使得CSS越来越模块化、结构化和灵活。但是想想你的icons。在icons上使用光栅图意味着他们不容易被css控制。如果我们需要去改变icon的hover、active、focus时候，我们应该怎么办呢？我们需要在种状态下的icon我们都需要去增加一个图片。另外对于图片的尺寸，每一个icon尺寸大小我们也都需要增加另外一个icon图片。对于图片的形状，阴影？我们都需要去对应增加图片数量。相当不方便对吧？

<!--more-->

但是幸运的是我们有icon字体去补救。icon字体允许我们去管理这些事更加的容易，其有以下益处：
* 它是纯文本，能够被压缩到95%;
* 它是矢量图，能够被设置成任何尺寸
* 它是资源文件，意味着最低限度的http请求
* 他是字体，你能够很容易的改变其大小、颜色、形状和增加阴影
* 兼容一些老版本的浏览器（比如ie6）

不幸的是，icon fonts有一些限制，比如单色调，并且我们只能使用文字样式比如一个text shadow来为图标增加阴影。我们不能使用文字内阴影或则在细节上使用不同的颜色。

因此我们今天探索使用SVG icons来取代它。

## 使用 SVG icons

我想分享给你们的icon解决方案是基于[SVG](https://tower.im/projects/a71aa70bbd464df6b420fb49351fadc5/)。其拥有icon fonts的好处外，还有一些额外超级能力：
* 稳定的跨浏览器系统的能力
* 我们会获得通过css修改我们icons的能力
* SVG[滤镜](http://www.w3.org/TR/SVG/filters.html)效果
* 和其他语言交互的能力，我们能够和SMIL、css和js配合使用[animations](http://www.w3.org/TR/SVG/animate.html)

当前看来。SVG看起来就像是与世隔绝的英雄。由于我们需求的原因（原博主好幽默～），让我们把他踢下沙发赶去健身房。（原博主幽默，意思就是让我们来用）

## 开始
首先，我们需要一些SVG文件。我假定你熟悉矢量图并知道如何创建或则得到SVG文件。看看[这篇](http://css-tricks.com/using-svg/)在CSS-Tricks的中介绍如何创建和使用SVGs的博客。对于我们这篇博客，我们将使用[这个svg](http://7fvhwe.com1.z0.glb.clouddn.com/icon_4.svg)文件。所有的例子你在这里看见的实际上都能在[pen](http://codepen.io/)上面运行，所以你能够自己去玩一玩实例。

我们已经有了一个矢量图片了，所以让我们加入一个空svg文件在一个body标签内。

我会成在整个博客中称这个SVG文件为“SVG源文件”（SVG源文件就是一个svg图片）。

让我们把这个“内部”SVG源文件加入到这个空的SVG描述当中然后给他一个ID用于后面通过xlink来引用。

```html
<!doctype html>
<html>
<head>
    <meta charset=utf-8 />
</head>
<body>
  <!-- SVG SOURCE -->
  <svg height="0" width="0" style="position:absolute;margin-left: -100%;">
    <path id="heart-icon" d="M256,465.559c0,0- 239.054-135.345-239.054-291.062c0-159
       .761,224.692-177.574,239.054-7.756 c17.244-169.692,239.054-152.008,239.054,
       7.756C495.054,330.214,256,465.559,256,465.559z"/>
  </svg>
  <!-- SVG SOURCE ends-->
<body>
<html>
```
[在codepen上有源码](http://codepen.io/sol0mka/pen/79f115c42ed01a5721717a9c67adc0b2)

如果你要构造很多SVG，那么你需要将他们用一个标签包起来形成一个组，并给这个组一个id。

现在我们可以“使用”这个图片了。无论在我们HTML文档的哪个地方。通过指定xlink:href属性来应用之前设置的svg。

```html
<!doctype html>
<html>
<head>
  <meta charset=utf-8 />
</head>
<body>

<!-- SVG SOURCE -->
...
<!-- SVG SOURCE ends -->

<svg class="icon" viewBox="0 0 32 32">
    <use xlink:href="#heart-icon">
</svg>

</body>

```

<p data-height="268" data-theme-id="17379" data-slug-hash="OVaZja" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/OVaZja/'>OVaZja</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>


是不是很酷～

> 在SVG文档中加入带id的任务元素节点，并在整个文档中复用。这个效果很像这个节点在一个未暴露的DOM中被复制，然后粘贴到他被引用的地方，类似于匿名内容和XBL。——[Mozilla DN](https://developer.mozilla.org/en-US/docs/Web/SVG/Element/use)

爱思考的读者可能会意识到在上面代码中最有趣的部分：我们能够简单的通过css去设置icons的样式。看下面的代码：


<p data-height="268" data-theme-id="17379" data-slug-hash="aOQGRw" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/aOQGRw/'>aOQGRw</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

我们能够对svg设置的属性真的是相当的[多](https://gist.github.com/legomushroom/7397561)。最有用也最平常的有这些属性：

* **width** 和 **height**
* icon颜色的设置是通过**fill**属性
* 文本或则元素的轮廓线（stroke）通过设置stroke和stroke-width设置([详细](http://www.runoob.com/svg/svg-stroke.html))

我们可能在很多地方使用这些属性。但现在，让我们使得其更好，来加入我们渴望的阴影吧。

## 加入滤镜效果

[滤镜效果](http://www.w3.org/TR/SVG/filters.html)是SVG真正的超级技能！如果你对其非常感兴趣可以看看这篇文章[Filter Effects](http://www.w3.org/TR/SVG/filters.html)和则这篇文章[SVG Filters by Mike Sierra](http://docs.webplatform.org/wiki/svg/tutorials/smarter_svg_filters)。

让我们实际的来试试一些SVG滤镜效果。

为了使用这些滤镜效果，我们需要先在我们的SVG源文件里面通过一个唯一的id声明，就像之前声明的icon一样，只不过用一个新的标签filter。

```html
<filter id='inset-shadow'>
<!-- Shadow offset -->
<feOffset
dx='0'
dy='0'
/>
<!-- Shadow blur -->
<feGaussianBlur
stdDeviation='1'
result='offset-blur'
/>
<!-- Invert drop shadow to make an inset shadow -->
<feComposite
operator='out'
in='SourceGraphic'
in2='offset-blur'
result='inverse'
/>
<!-- Cut color inside shadow -->
<feFlood
flood-color='black'
flood-opacity='.95'
result='color'
/>
<feComposite
operator='in'
in='color'
in2='inverse'
result='shadow'
/>
<!-- Placing shadow over element -->
<feComposite
operator='over'
in='shadow'
in2='SourceGraphic'
/>
```

然后我们就可以在其他地方加入它的引用了。

```html
<svg viewBox="0 0 32 32">
  <g filter="url(#inset-shadow)">
    <use xlink:href="#heart-icon"></use>
  </g>
</svg>
```

仔细看看下面的SVG滤镜效果代码：

* **内阴影**

<p data-height="268" data-theme-id="17379" data-slug-hash="waQYxW" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/waQYxW/'>waQYxW</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

* **阴影**

<p data-height="268" data-theme-id="17379" data-slug-hash="rVQqrd" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/rVQqrd/'>rVQqrd</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

* **更多的例子**

<p data-height="368" data-theme-id="17379" data-slug-hash="OVaBaN" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/OVaBaN/'>OVaBaN</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

是不是很酷？

## 加入交互

就像我们一开始提到的，我们能使用SMIL（可标记语言），CSS或则js制作有生机的icons。

所以让我们来给icons一点生命。我们会创建一个动画时钟并具有我们能够赋予的动画效果。

我们会以一个闹钟icon作为我们的SVG源文件来开始这项工作。我们构建一个组合它们包含很多不同的形状，并给他一个ID “#clock-cion”作为引用标记。

<p data-height="368" data-theme-id="17379" data-slug-hash="pJqoaO" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/pJqoaO/'>pJqoaO</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

icon来自[visualpharm](http://www.visualpharm.com/),许可证：[CC by-nd 3.0](http://creativecommons.org/licenses/by-nd/3.0/)

我同样会增加一些其他的IDs比如 **#hour-hand**,**minute-hand**和**second-hand**，主要是用于动画操作引用。

现在，让我们为时针、分针和秒针都申明一个2D旋转动画。

我们会加一个值为rotate(0 16 17)的transform（动画），因为：

* 0是旋转的时钟指针的度数
* 17 16只不过是我们在轴和轴上旋转的中心

因此，使用如下旋转值：
```
<rect id="hour-hand" transform="rotate(320 16 17)" x="15.386" y="10.291" width="1.227" height="7.626"/>
```

我们将旋转我们的指针320度。
看看这个例子：

<p data-height="368" data-theme-id="17379" data-slug-hash="jPXOxK" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/jPXOxK/'>jPXOxK</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

接下来的步骤就是加入读书旋转的动画了。我们需要使用到js并用其控制在每一秒rotate的变化：

```js
<script>
    var setTime = function(){
      var date = new Date(),
      MINUTE = 60, HOUR   = 60*MINUTE,
      seconds = date.getSeconds(),
      minutes = (date.getMinutes()*MINUTE) + seconds,
      hours = (date.getHours()*HOUR)+minutes;

      document.getElementById('second-hand').setAttribute('transform', 'rotate('+360*(seconds/MINUTE)+',16,17)');
      document.getElementById('minute-hand').setAttribute('transform', 'rotate('+360*(minutes/HOUR)+',16,17)');
      document.getElementById('hour-hand').setAttribute('transform', 'rotate('+360*(hours/(12*HOUR))+',16,17)');
    }
    setTime();
    var interval = setInterval(setTime,1000);
</script>
```

<p data-height="368" data-theme-id="17379" data-slug-hash="zGyYjV" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/zGyYjV/'>zGyYjV</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

看起来不错吧？（原博主很喜欢这句话）

如果你对使用SMIL（同步多媒体集成语言）版本的例子感兴趣，可以看看这个[例子](http://codepen.io/sol0mka/pen/0bf400db555f7c9701387b73d25c80a7)。注意到SMIL语言在有些浏览器是[不兼容的](http://caniuse.com/#feat=svg-smil)。

## 使用媒体查询

我们能够使用单独的css尤其是媒体查询在我们的icon图片中。意思就是我们有能力去改变其属性，比如我们能够高孔子icon的形状通过屏幕大小或则其他。比如我们将使用这样的内联样式：

```html
<style>
   <![CDATA[     @media screen and (max-width:810px){      .hide{        display: none;      }    }   ]] >
</style>
```

尝试调整浏览器大小，查看下面这段代码在少于800像素是的呈现。

<p data-height="368" data-theme-id="17379" data-slug-hash="aOPbRb" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/aOPbRb/'>aOPbRb</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

有没有发现钟的样式有变化。

## SVG icons的更多乐趣

让我们使用我们学习过的东西来做一些更有趣的事。下图的例子是不同的icon和一些js动画效果：

<p data-height="368" data-theme-id="17379" data-slug-hash="YXdzJp" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/YXdzJp/'>YXdzJp</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

下面的例子我们会增加一些滤镜效果：

<p data-height="368" data-theme-id="17379" data-slug-hash="mJadzm" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/mJadzm/'>mJadzm</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

这么多超级特效，创作限制你的只是你的想象力。

## 浏览器支持

我们今天指出的这些在左右流行的主流浏览器是支持的：
* IE9（滤镜效果从IE10开始支持）
* Mozilla 4+
* Opera 11.6+(滤镜效果从Opera12开始)
* Safari 5.1+(滤镜效果从Safari6开始)
* Chrome 14+ 

## 性能表现

在进行了一些测试后，我们发现SVG icons在chrome下比.wof加载时间少两倍。第一副图片的每个icon的计算需要0.05毫秒，而用户看到30个icons平均需要1.453毫秒。

| icons的数量   | 计算时间（毫秒） | 每个icons计算时间 |
| --------------- | -------------------- | --------------------- |
| 1               | 0.059                | 0.059                 |
| 30              | 1.453                | 0.04843               |
| 90              | 4.373                | 0.04858               |
| 180             | 9.315                | 0.05175               |
| 3000            | 32.131               | 0.01071               |


这些测试都是在没有任何滤镜效果下或则交互的情况下做的测试。

## 结论

让我们回头看看并分析下我们做了些啥。

使用SVG作为icons在我们的web项目中，将给我门带来如下优势：
* 只有一个HTTP请求
* 可缩放的矢量图，所以从本质上讲适应视网膜屏
* 很容易用css控制其颜色、大小和特效
* SVG icons是比较轻的（文件相对较小）
* 他是文本元素所以他能够被压缩到95%
* 滤镜特效
* 类似XML的结构
* 我们能够有多色的icons
* 我们能使用单独的css和媒体查询
* 我们能够使用SMIL、CSS、JS添加单独的动画
* 支持所有主流浏览器

## iconmelon计划

![](http://7fvhwe.com1.z0.glb.clouddn.com/6f3c6b09bd2ab7c7be10f3759a0e5b8e.png)

这个是原博主的一个开源项目，[iconmelon](http://iconmelon.com/#/page-1)读者可以自己了解下。






** 本文翻译自[SVG Icons FTW](http://tympanus.net/codrops/2013/11/27/svg-icons-ftw/),如果有问题请在评论中指出,谢谢 **

