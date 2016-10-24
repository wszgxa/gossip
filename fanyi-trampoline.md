title: 好玩的蹦床效果
subtitle: 日常翻译六
tags: 
- HTML5
- 前端
- 翻译
- 设计
layout: post
categories: HTML5
date: 2015-09-10
---

[demo和原文在这](http://tympanus.net/codrops/2015/03/04/playful-trampoline-effect/)

今天分享一个有趣的小动画。主要的想法是在切换导航（navigating）图片时模拟蹦床的弹性效果。其中的图片拖拽效果我们使用的是[Elastic STack](http://tympanus.net/codrops/2013/11/12/elastic-stack-elastic-dragging-interaction/)。而svg的动画，我们使用了[snap.svg](http://snapsvg.io/)。当然我们在整个过程中会使用css 的transitions属性。

demo中的图片使用得是Leonard Nimoy，是为了向他致敬，其上周逝去了。（外国程序员好有情怀）
<!--more-->

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/03/PlayfulTrampolineEffect01.png)

最好看看[Elastic Stack](http://tympanus.net/codrops/2013/11/12/elastic-stack-elastic-dragging-interaction/)的教程并理解其如何工作。而Elastci Stack使用了David Desandro的[Draggabilly](http://draggabilly.desandro.com/)。

在body里面的第一个元素是SVG形状元素，在我们切换图片时我们将控制它从一个矩形变成挤压后的矩形。

```html
<div id="morph-shape" class="morph-shape" data-morph-next="M301,301c0,0-83.8-21-151-21C71.8,280-1,301-1,301s21-65.7,21-151C20,79.936-1-1-1-1s73,11,151,11c85 0,151-11,151-11s-21,66.43-21,151C280,229.646,301,301,301,301z">
  <svg width="100%" height="100%" viewBox="0 0 300 300" preserveAspectRatio="none">
	<path d="M301,301c0,0-83.8,0-151,0c-78.2,0-151,0-151,0s0-65.7,0-151C-1,79.936-1-1-1-1s73,0,151,0c85,0,151,0,151,0s0,66.43,0,151
C301,229.646,301,301,301,301z" />
  </svg>
</div>
```

初始的矩形定义在svg的path中，然后我们将压缩后的矩形形状存储在data-morph-next中，以便后面动画使用。

照片堆的html结构：

```html
<div class="stack">
	<ul id="elasticstack" class="stack__images">
		<li><img src="img/1.jpg" alt="01"/></li>
		<li><img src="img/2.png" alt="02"/></li>
		<li><img src="img/3.jpg" alt="03"/></li>
		<li><img src="img/4.jpg" alt="04"/></li>
		<li><img src="img/5.png" alt="05"/></li>
		<li><img src="img/6.png" alt="06"/></li>
	</ul>
	<button id="stack-next" class="stack__next"><i class="icon icon-down"></i><span>Next</span></button>
	<ul id="stack-titles" class="stack__titles">
		<li class="current">
			<blockquote>
			  <p>"Once you have eliminated the impossible, whatever remains, however improbable, must be the truth."</p>
			  <footer><a href="http://drbl.in/nTZA">#RIPLeonardNimoy</a> by James Olstein</footer>
			</blockquote>
		</li>
		<li>
			<blockquote>
			  <p>"The needs of the many outweigh the needs of the few, or the one."</p>
			  <footer><a href="http://drbl.in/nUqE">Mr. Spock</a> by Mustafa Kural</footer>
			</blockquote>
		</li>
		<li>
			<blockquote>
			  <p>"Insufficient facts always invite danger."</p>
			  <footer><a href="http://drbl.in/nUhf">LLAP</a> by Sarah McKay</footer>
			</blockquote>
		</li>
		<li>
			<blockquote>
			  <p>"Without followers, evil cannot spread."</p>
			  <footer><a href="http://drbl.in/nTTO">RIP Leonard Nimoy</a> by derric</footer>
			</blockquote>
		</li>
		<li>
			<blockquote>
			  <p>"Logic is the beginning of wisdom, not the end."</p>
			  <footer><a href="http://drbl.in/nUcJ">#Goodnight, Spock</a> by Helen Tseng</footer>
			</blockquote>
		</li>
		<li>
			<blockquote>
			  <p>"Change is the essential process of all existence."</p>
			  <footer><a href="http://drbl.in/nTYY">RIP Spock</a> by Sahirul Iman</footer>
			</blockquote>
		</li>
	</ul>
</div><!-- /stack -->
```

上面构成了图片堆(image stack),一个导航按钮去显示下一张图片，和每个图片的一句描述的话。我们适当调整了Elastic stack，使得能够将标题从image元素中提取出来，从而能在其上应用一些不同的效果。

前面的svg变换图形，我们需要将它延伸到整个屏幕。

```css
.morph-shape {
	position: absolute;
	width: 100%;
	height: 100%;
	top: 0;
	left: 0;
}

.morph-shape svg {
	position: absolute;
	margin: 0;
	pointer-events: none;
}
```

而加在图片堆上面的样式是我们改边后的Elastic Stack样式。我们在对ul中的images设置[perspective](http://tympanus.net/codrops/css_reference/perspective/)(建议了解下，3d效果会经常用到)值，这样我们就能在3d空间中定位它。下面最后两个类将会被动态加入，是我们用来创建动画效果的。

```css
.stack ul {
	position: relative;
	margin: 0 auto;
	padding: 0;
	list-style: none;
}

.stack ul li {
	position: absolute;
	width: 100%;
	opacity: 0;
}

ul.stack__images {
	width: 400px;
	height: 300px;
	z-index: 10;
	perspective: 1000px;
	perspective-origin: 50% -50%;
}

@media screen and (max-height: 530px), screen and (max-width: 400px) {
	ul.stack__images {
		width: 260px;
		height: 195px;
	}
}

.stack__images li {
	top: 0;
	z-index: 1;
	transform: translate3d(0, 0, -180px);
	transform-style: preserve-3d;
}

.stack__images li img {
	display: block;
	max-width: 100%;
	pointer-events: none;
}

.stack__images li:hover {
	cursor: url(../img/cursor_vulcan.png), auto;
}

.stack__images li:active {
	cursor: -webkit-grabbing;
	cursor: grabbing;
}

.stack__images li.animate {
	transition: all 0.3s ease-out;
}

.stack__images li.move-back {
	transition-timing-function: cubic-bezier(0.175, 0.885, 0.470, 1.515);
}
```

对于其他得元素，比如导航按钮还有标题的样式：

```css
.stack__next {
	border: none;
	background: none;
	display: block;
	padding: 0;
	overflow: hidden;
	width: 36px;
	height: 36px;
	margin: 10px auto 0;
	font-size: 30px;
	position: relative;
	cursor: pointer;
	color: #067ba7;
}

.stack__next:hover {
	color: #fff;
}

.stack__next:focus {
	outline: none;
}

.stack__next span {
	position: absolute;
	top: 200%;
}

ul.stack__titles {
	height: 18vh;
	max-width: 560px;
	width: 95%;
}

.stack__titles blockquote {
	margin: 0;
	text-align: center;
	font-size: 1.4em;
}

.stack__titles blockquote footer {
	font-size: 50%;
	padding-bottom: 1em;
	font-family: 'Montserrat', Arial, sans-serif;
}

.stack__titles li {
	pointer-events: none;
	transition: opacity 0.45s ease;
}

.stack__titles li.current {
	opacity: 1;
	pointer-events: auto;
}
```

标题元素在变换时渐入渐出。

如果你是Yosemite系统这里的光标可能会有一个[问题](http://apple.stackexchange.com/questions/151370/my-cursor-isnt-changing-to-a-pointer-when-hovering-in-safari)。

最后，我们需要做的就是在样式表中定义一些[transitions](http://tympanus.net/codrops/css_reference/transition/)。当我们执行这个蹦床效果时，SVG将会改变它的填充大小，同时我们主要的内容区（container）将会使用[3D transform](http://tympanus.net/codrops/css_reference/transform/)在z轴上做3D变换从而看起来更小。对于container这段变换的样式你可以在base.css中找到。

![](http://codropspz.tympanus.netdna-cdn.com/codrops/wp-content/uploads/2015/03/PlayfulTrampolineEffect02.jpg)


当我们点击变换的按钮（navigate）时，我们将navigate-next这个类加给body。在demo2中我们加入了一个微小的变化，我们将内容区（container）在z轴上旋转10度，在x轴上旋转-5度。

```css
.morph-shape svg {
	fill: #01AEF0;
	transition: fill 0.1s ease-out;
}

.navigate-next .morph-shape svg {
	fill: #01a0dc;
	transition-duration: 0.45s;
}

.container {
	transition: transform 0.1s cubic-bezier(0.6, 0, 0.5, 1);
}

.demo-1.navigate-next .container {
	transition-duration: 0.45s;
	transform: translate3d(0, 0, -600px);
}

.demo-2.navigate-next .container {
	transition-duration: 0.45s;
	transform: rotate3d(-0.5, 0, 1, -6deg) translate3d(0, 0, -600px);
}

.demo-2 .morph-shape svg {
	fill: #A2CD72;
}

.demo-2.navigate-next .morph-shape svg {
	fill: #95C264;
}
```

对于javascript部分，我们需要包含三个外部库：snap、modernizr、Draggabilly[http://draggabilly.desandro.com/]。另外还需要一部分[Elastic Stack](http://tympanus.net/codrops/2013/11/12/elastic-stack-elastic-dragging-interaction/)的代码。
然后我们定义好，我们的代码，管理好SVG图形变换和导航键点击之间的逻辑：

```js
(function() {
	var body = document.body,
		titles = [].slice.call( document.querySelectorAll( '#stack-titles > li' ) ),
		totalTitles = titles.length,
		stack = new ElastiStack( document.getElementById( 'elasticstack' ), {
			onUpdateStack : function( idx ) {
				classie.remove( titles[idx === 0 ? totalTitles - 1 : idx - 1], 'current' );
				classie.add( titles[idx], 'current' );
			}
		} ),
		shapeEl = document.getElementById( 'morph-shape' ),
		s = Snap( shapeEl.querySelector( 'svg' ) ),
		pathEl = s.select( 'path' ),
		paths = { 
			reset : pathEl.attr( 'd' ),
			next  : shapeEl.getAttribute( 'data-morph-next' )
		};

	document.getElementById( 'stack-next' ).addEventListener( 'mousedown', nextItem );
	
	function nextItem() {
		classie.add( body, 'animating' );
		classie.add( body, 'navigate-next' );
		pathEl.stop().animate( { 'path' : paths.next }, 450, mina.easeinout, function() {
			classie.remove( body, 'navigate-next' );
			stack.nextItem( { transform : 'translate3d(0,-60px,400px)' } );
			pathEl.stop().animate( { 'path' : paths.reset }, 100, mina.easeout, function() {
				classie.remove( body, 'animating' );
			} );
		} );
	}
})();
```
ok，搞定了。




** 本文翻译自[Playful Trampoline Effect](http://tympanus.net/codrops/2015/03/04/playful-trampoline-effect/),如果有问题请在评论中指出,谢谢 **
