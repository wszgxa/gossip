title: 使用SVG
subtitle: 日常翻译二
tags: 
- HTML5
- SVG
- 翻译
layout: post
categories: HTML5
date: 2015-08-04
---


SVG 是一个矢量图形格式。它文字的意思是可扩展矢量图形（Scalable Vector Graphics）。基本上说，就是你Adobe Illustrator上面使用的。你能够在web上轻松的使用SVG图标，而这里有很多你需要了解的知识。

## 到底为啥要使用SVG？

* 在压缩后文件大小比较小
* 能被放缩到任何大小，而且不会失掉清晰度（及其小除外）
* 在视网膜屏上表现良好
* 能够控制一些特效样式，比如交互和阴影
<!--more-->
## 如何获得SVG图片

在Adobe Illustrator设计一些东西。比如像下图在一个椭圆阴影上的Kiwi鸟。

![kiwi bird](http://7fvhwe.com1.z0.glb.clouddn.com/kiwi.png)

注意到这个画布边框抵着整个设计的边缘。就像在PNG或则JPG中一样，这在SVG画布上也很重要。（这点没翻译好）

你能够直接从Adobe Illustrator中保存为SVG文件。

![save as svg](http://7fvhwe.com1.z0.glb.clouddn.com/save-as-svg.png) 

在你保存它的时候，你会收到一个设置SVG选项的对话框。我真的不太了解这些设置选项。这里有个关于[SVG选项](http://www.w3.org/TR/SVGMobile/)的完整说明。我发现SVG 1.1很好用。

![svg options](http://7fvhwe.com1.z0.glb.clouddn.com/svg-options.png)

这里比较有趣的时你能够点击ok去保存这个文件，或者使用“SVG Code...”然后他会打开一个文本编辑器，SVG代码就在里面显示着。

![svg code](http://7fvhwe.com1.z0.glb.clouddn.com/svg-code.png)

两者都很有用。

## 在`<img>`中使用SVG

如果上面保存为SVG文件，我能够在html中直接使用`<img>`标签。

```html
<img src="kiwi.svg" alt="Kiwi standing on oval">
```

在 Illustrator中，我们的画板时612px*502px。

那就是这个image会呈现在页面上的大小，就像下图左面的那个大小。你能够通过css选择`img`标签设置`width`和`height`来改变它的大小，就和PNG和JPG图片一样。这里有一个例子如下：


<p data-height="368" data-theme-id="17379" data-slug-hash="YXdMar" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/YXdMar/'>YXdMar</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

## 浏览器支持

这种方式使用SVG是有明确的[浏览器支持的](http://caniuse.com/#feat=svg-img)。但是其本质上是和使用img时一样的。仅仅的问题是IE8及以下和安卓2.3及一下不支持。

Modernizr(一个js库)能够帮助我们，能使得我们以一种更高效的方式使用img。如果我们用一种支持的格式代替`background-image`，就只需要一个HTTP请求而不需要两个。Modernizr加入一个“no-svg”的class给这个html元素，如果它不支持SVG，我们就使用它：

```css
.main-header {
  background: url(logo.svg) no-repeat top left;
  background-size: contain;
}

.no-svg .main-header {
  background-image: url(logo.png);
}

```

另外一种对于使用SVG作为图片兼容问题比较机智的改进是通过使用多重的背景。SVG和多重背景有类似的浏览器支持，所以如果浏览器支持多重背景，它就支持SVG，然后SVG的声明就会有用（然后覆盖前面的声明）。

```css
body {
  background: url(fallback.png);
  background-image: url(image.svg), none;
}
```

## 同时使用`<img>`和background-image...

如果

## 使用"inline"SVG

还记得如何抓取SVG code从Illustrator吗？（你也可以用一个文本编辑器打开一个SVG文件然后复制里面的代码。）你能够将这个代码放到一个HTML文档中，然后这个SVG图片也能呈现出来，就和你放在一个img标签里的一样。

```html
<body>

   <!-- paste in SVG code, image shows up!  -->

</body>
```

这样很好因为这个image直接就在文档中，不需要额外的HTTP请求。换句话说，它和使用[Data URI](https://css-tricks.com/data-uris/)有同样的好处。它也有同样的坏处。一个极度“浮肿”的文档，一大块废话放在文档中，对于查看代码是一件及其烦人的事情,另外它也不像img一样会被浏览器缓存。

如果你在使用一中后端语言能够回滚获取文件并插入到文档中，你就能解决这个问题了。例如：

```php
<?php echo file_get_contents("kiwi.svg"); ?>

```

这里使用了一个php小特性。显然`file_get_contents()`在着是一个最适合的函数，而使用`include()`或者`include_once()`就不是很好。尤其因为SVG有时会以`<?xml version="1.0" encoding="UTF-8"?>`作为初始行，这回导致PHP解析出错并终止。

## 首先优化SVG图片

可能不是特别的令人震惊，但是Adobe Illustrator给我们的图片并不是压缩过的。它还带有一个DOCTYPE和生产注释(generator notes)和一些废话。SVG已经时相当小了，但是我们能让他更小，为啥不做呢？Peter Collingridge 有个在线[SVG优化](http://petercollingridge.appspot.com/svg_optimiser)工具。你只需要上传旧的SVG图片下载新的就行。在[Kyle Foster's video](https://www.youtube.com/watch?v=iVzW3XuOm7E&feature=youtu.be)中，他做的更好，它甚至删除了换行。如果你更厉害，这还有一个[Node JS tool](https://github.com/svg/svgo)。

## 现在你能够开始用css控制它了

我们发现SVG看起来有点像HTML？那是因为它本质上就是XML(用尖括号包起来的标签，然后里面填充上内容)。在我们的设计中，我们需要两个元素：一个是`<ellips>`另外一个时`<path>`。我们能够在他们内添加class，就像其他的HTML元素一样。

```html
<svg ...>
  <ellipse class="ground" .../>
  <path class="kiwi" .../>
</svg>
```

现在我们能在这个页面的任何css中控制这个独立的元素并使用特别的SVG CSS。这不是必须嵌入进SVG文件中的，我们能在任何CSS文件中指定它，甚至在全局样式表`<link>`内容内。注意SVG元素有一些特别的CSS性质。比如，它没有`background-color`，它是`fill`。不过你也能使用一些正常的性质比如hover。

```css
.kiwi {
  fill: #94d31b; 
}
.kiwi:hover {
  fill: #ace63c; 
}
```

更酷的是，SVG有梦幻般的滤镜效果，比如模糊。加入一个filter在你的`<svg>`当中：

```html
<svg ...>
  ...
  <filter id="pictureFilter" >
    <feGaussianBlur stdDeviation="5" />
  </filter> 
</svg>
```

然后你就能够在你的css中应用了：

```css
.ground:hover {
  filter: url(#pictureFilter);
}
```

这里有个例子：

<p data-height="368" data-theme-id="17379" data-slug-hash="GJzZeV" data-default-tab="result" data-user="hiluluke" class='codepen'>See the Pen <a href='http://codepen.io/hiluluke/pen/GJzZeV/'>GJzZeV</a> by hiluluke (<a href='http://codepen.io/hiluluke'>@hiluluke</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

* [More on applying filters to SVG](https://developer.mozilla.org/en-US/docs/Applying_SVG_effects_to_HTML_content)
* [The best list i could find on SVG-specific CSS properties](http://www.opera.com/docs/specs/presto25/svg/cssproperties/)


## 浏览器支持

行内SVG有它自己的浏览器[兼容表](http://caniuse.com/#feat=svg-html5),但是和前面一样，它本质上只在IE8和安卓2.3及以下有问题。

一种解决办法是[回退法](https://css-tricks.com/svg-fallbacks/):

```html
<svg> ... </svg>
<div class="fallback"></div>
```

然后再使用Modernizr:

```css
.fallback { 
  display: none;
  /* Make sure it's the same size as the SVG takes up */
}
.no-svg .fallback { 
  background-image: url(logo.png); 
}
```

## 在`<object>`里使用SVG

如果一个行内SVG还是不对你的胃口（行内SVG会显得很臃肿），你还可以用`<object>`标签来引入SVG，同时用CSS来给它增加样式。

```html
<object type="image/svg+xml" data="kiwi.svg" class="logo">
  Kiwi Logo <!-- fallback image in CSS -->
</object>

```

对于功能回退，[Modernizr detection](http://modernizr.com/)在这表现很好：

```css
.no-svg .logo {
  width: 200px;
  height: 164px;
  background-image: url(kiwi.png);
}
```

这种方法很适合写在HTML中（能够被浏览器缓存），另外相对与其他方法有更广泛的浏览器支持。但是如果你想让CSS文件工作，你就不能在文档用外联CSS文件或则用`<style>`在document中直接引用CSS文件，你需要将`<style>`放到SVG文件中去。

```html
<svg ...>
  <style>
    /* SVG specific fancy CSS styling here */
  </style>
  ...
</svg>
```

### `<object>`SVG额外的样式表

SVG有一个方式去声明一个额外的样式表，它有如下好处：好声明、利于存储。在我测试后发现只有SVG文件嵌入到`<object>`当中才会有效。你需要将将下面的代码放到SVG文件中`<svg>`标签上方。

```html
<?xml-stylesheet type="text/css" href="svg.css" ?>
```

如果你将其放在你的HTML中，这个页面将会呕吐(barf，一种修辞手法吧。。)并不会尝试去渲染SVG。

## [Data URI's](http://baike.baidu.com/link?url=12h9rR2QRPGWmGRoo3UsfxFM7J1BuJ1wvBmKrKrwZp7ANdBOBk5uS_dXvYutPhwSis0qmEBy2Dm3dpJ-FZyMXK) for SVG

另外一种方法去使用SVG是去将它们转码成Data URI。Data URI将不会保持实际的文件尺寸，但是它将更加的高效，因为数据直接就在。它并不需要额外的网络请求。
Mobilefish.com有一个SVG转Data URI的[在线转换工具](http://www.mobilefish.com/services/base64/base64.php)。简单的将你的SVG文件内容粘贴到文本框点击转换后结果就呈现在呈现在文本框，你可以复制其。记得去掉将给你的结果中的换行去掉。结果看起来就像胡言乱语：

![](http://7fvhwe.com1.z0.glb.clouddn.com/base64-data.png)

你能够将之用于任何地方（除了行内`<svg>`因为它根本没有意义）。只管将这段胡言乱语放到有[data]的地方，就像下面的例子：

### 在`<img>`中

```html
<img src="data:image/svg+xml;base64,[data]">
```

### 在CSS中

```css
.logo {
  background: url("data:image/svg+xml;base64,[data]");
}
```

### 在`<object>`中

```html
<object type="image/svg+xml" data="data:image/svg+xml;base64,[data]">
  fallback
</object>
```

### Data URI的数据格式

所有上面这些例子都把base64作为编码格式，但是data URI不只有
这几种编码格式。实际上，对于SVG我们最好不要用base64编码格式。首先因为本地编码的SVG比base64的更加啰嗦，它压缩的会更好。

```html
<!-- base64 -->
data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL...

<!-- UTF-8, not encoded -->
data:image/svg+xml;charset=UTF-8,<svg ...> ... </svg>

<!-- UTF-8, optimized encoding for compatibility -->
data:image/svg+xml;charset=UTF-8,%3Csvg xmlns='http://...'

<!-- Fully URL encoded ASCII -->
data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A//...

```

base64ing SVG的命令行小工具：

> Use openssl base64 < path/to/file.png | tr -d '\n' | pbcopy or cat path/to/file.png | openssl base64 | tr -d '\n' | pbcopy to skip writing to a file and just copy the base64-encoded output to the clipboard without the line breaks.


## 自动化工具

* grunticon：

> 从css看来，其非常好用，只需要给每个icon一个引用的class，并不使用css sprites。
> grunticon 从一个文件夹获取SVG/PNG，然后返回给CSS 3种形式的引用方式：SVG data urls、png data urls、和一个CSS功能回滚文件指向一个PNG图像文件（它被自动产生并放在一个文件夹里面）。


* iconizr
> 一个php的命令行工具，功能时将SVG图片转换城一些CSS icons（SVG和PNG，单独的icons和CSS sprites），它支持图像优化和生成Sass文件。


## 相关的文章

* David Bushell: [A Primer to Front-end SVG Hacking](http://dbushell.com/2013/02/04/a-primer-to-front-end-svg-hacking/)
* David Bushell: [Resolution Independence With SVG](http://www.smashingmagazine.com/2012/01/resolution-independence-with-svg/)
* [MDN on SVG](https://developer.mozilla.org/en-US/docs/Web/SVG)
* Browser support for [a variety of different SVG related things.](http://caniuse.com/#search=svg)
* Peter Gasston: [Better SVG Sprites With Fragment Identifiers](http://www.broken-links.com/2012/08/14/better-svg-sprites-with-fragment-identifiers/)
* [SVG.js](http://svgjs.com/) - "A lightweight library for manipulating and animating SVG."
* Emmet has [an awesome way](http://docs.emmet.io/actions/base64/) to get a data URI from an SVG right from your code editor.
* Compass [has a helper](http://compass-style.org/reference/compass/helpers/inline-data/) for data URIs too.
Adobe: [Styling SVG](http://blogs.adobe.com/webplatform/2013/01/08/svg-styling/)
Andrew J. Baker: [Taming the SVG Beast](http://buildnewgames.com/taming-the-svg-beast/)
Krister Kari: [Dealing with SVG images in mobile browsers](http://kristerkari.github.io/adventures-in-webkit-land/blog/2013/03/08/dealing-with-svg-images-in-mobile-browsers/)







** 本文翻译自[Using SVG](https://css-tricks.com/using-svg/),如果有问题请在评论中指出,谢谢 **

