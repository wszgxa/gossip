title: 如何创建一个平铺背景滑动效果
subtitle: 日常翻译五
tags:
- HTML5 
- 设计
- 前端
- 翻译
layout: post
categories: HTML5
date: 2015-09-3
---


今天我们将看看如何实现像[Serge Thoroval’s Atelier](http://www.atelier-serge-thoraval.com/fr/)中的全屏背景滑动特效。
[demo和原来译文](http://tympanus.net/codrops/2015/03/04/playful-trampoline-effect/)

## 标签

为了达到这个滚动效果，我们需要一个能让我们构造4个不同“瓷砖”效果的特别标签结构，它们将使用同一张图片的不同部分。因为我们这是一个全屏滚动效果，所以我们需要确保让所有元素都延伸至全屏。为了使得我们能制定那个图片被可见，我们需要定义一个简单的初始结构并构建我们“瓷砖”的重复结构。初始结构就像这样：

```html
<div id="boxgallery" class="boxgallery" data-effect="effect-1">
	<div class="panel"><img src="img/1.jpg" alt="Image 1"/></div>
	<div class="panel"><img src="img/2.jpg" alt="Image 2"/></div>
	<div class="panel"><img src="img/3.jpg" alt="Image 3"/></div>
	<div class="panel"><img src="img/4.jpg" alt="Image 4"/></div>
</div>
```

为了去将一张图片分成4个小瓷砖，我们为每个面板构建如下的重复结构：

```html
<div id="boxgallery" class="boxgallery" data-effect="effect-1">
	<div class="panel current">
		<div class="bg-tile">
			<div class="bg-img"><img src="img/1.jpg" /></div>
		</div>
		<div class="bg-tile">
			<div class="bg-img"><img src="img/1.jpg" /></div>
		</div>
		<div class="bg-tile">
			<div class="bg-img"><img src="img/1.jpg" /></div>
		</div>
		<div class="bg-tile">
			<div class="bg-img"><img src="img/1.jpg" /></div>
		</div>
	</div>
	<div class="panel">
		<div class="bg-tile">
			<div class="bg-img"><img src="img/2.jpg" /></div>
		</div>
		<div class="bg-tile">
			<div class="bg-img"><img src="img/2.jpg" /></div>
		</div>
		<div class="bg-tile">
			<div class="bg-img"><img src="img/2.jpg" /></div>
		</div>
		<div class="bg-tile">
			<div class="bg-img"><img src="img/2.jpg" /></div>
		</div>
	</div>
	<div class="panel">
		<!-- ... -->
	</div>
	<div class="panel">
		<!-- ... -->
	</div>
	<nav>
		<span class="prev"><i></i></span>
		<span class="next"><i></i></span>
	</nav>
</div>

```
<!--more-->

当然我们将会加上两个个按钮，这样浏览器才能实现动画。我们这里定义了data属性`data-effect`将会使我们定义一些不同的变化效果。

自然的，你会思考这个结构，然后你会问：为什么不使用background image 取代image元素？在尝试了很多次跨浏览器调试之后，我们得出了一个结论：在使用背景图片同时使用`background-size`	同时会导致波浪起伏的（掉帧）渐变效果。比如，当使用`background-size:cover`会导致动画效果抽搐。另外一个会导致抽搐渐变的是在执行渐变时使用百分比。第一眼看上去可能不会注意到，但是当使用像素的动画效果比较，你就会看到巨大的[差别](http://jsfiddle.net/crnacura/rmZ7B/5/)了。出于上述原因：我们将不再使用classes来控制动画变换，我们将用js脚本来控制视窗大小，并将相应的视窗大小用像素单位加在我们的瓷砖上。

接下来加入一些样式给他们。

## CSS部分

在我们脑中已经构建了一个结构，现在需要得是加入样式。


首先，我们是处于“全屏”模式，所以我们需要先让我们得页面元素准备好这。设置`html`和`body`和主要得conatiner高度为100%将会允许我们扩张我们得滚动效果为铺满全视口的：

```css
html, body, .container {
	height: 100%;
}
```

主要得包含块和子块都赋予绝对定位，而面板将会占住所有得宽和高。

```css
.js .boxgallery,
.js .boxgallery div {
	position: absolute;
}

.js .boxgallery,
.js .panel {
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
}
```

因为所有的动画特效将可能导致元素溢出到限制之外，所以我们将确保没有任何东西溢出：

```css
.js .boxgallery,
.bg-tile,
.bg-img {
	overflow: hidden;
}
```

当我们看Serge Thoroval的滚动特效时，你会发现在换屏动画的过程中，新显示的背景也会滑动进入。我们将会使用3D转换，所以我们需要设置透视（perspective）以使得我们能够在z轴能够对起产生的变换对我们可见：

```css
.panel {
	z-index: 0;
	perspective: 1200px;
}
```

接下来，我们需要给“瓷砖”片设置样式。因为我们需要4个瓷砖片，所以我们给每个都设置50%的高和宽。

```css
.bg-tile {
	width: 50%;
	height: 50%;
}
```

其他得“瓷片”：

```css
.bg-tile:nth-child(2),
.bg-tile:nth-child(4) {
	left: 50%;
}

.bg-tile:nth-child(3),
.bg-tile:nth-child(4) {
	top: 50%;
}
```

内部带有class为**bg-img**的图片将会填充整个瓷片:

```css
.bg-img {
	width: 100%;
	height: 100%;
	background: #999;
}
```

为了让每个瓷片的图片都完全展现整个屏幕，我们需要设置img的高度和宽度是原先的两倍。如果对高度和宽度都使用百分比，高度和和宽度会将我们得图片扭曲，所以我们需要确保方向比例。我们能够只设置其中一个尺寸为200%，而另外一个定义为自动调节。但是，我们设置哪个呢？这取决于视窗是更宽还是更高。幸运的是，我们可以使用媒体查询，然后恰恰有一个属性可以帮我们做这件事:min-aspect-ratio。这个属性允许我们拿图片真正的大小作为比例并定义，所以我们可以自动计算调整以什么为百分比：

```css
.bg-img img {
	position: absolute;
	display: block;
	height: 200%;
}

@media screen and (min-aspect-ratio: 1280/850) {
	.bg-img img {
		width: 200%;
		height: auto;
	}
}
```

你能够使用上面的这个方法在其他需要调节图片与屏幕比例的地方。

每个图片都需要定位：

```css
.bg-tile:nth-child(2) .bg-img img,
.bg-tile:nth-child(4) .bg-img img {
	left: -100%;
}

.bg-tile:nth-child(3) .bg-img img,
.bg-tile:nth-child(4) .bg-img img {
	top: -100%;
}
```


两边的button箭头使用[Arrow Navigation Styles](http://tympanus.net/Development/ArrowNavigationStyles/)中的“Fill Path”样式。、

接下来，让我们定义当我们翻页（navigate）时发生什么？

首先，我们需要设置current panel的z-index，让它处于我们其他元素得上面。因为我们设置了所有得（panel）面板得z-index为0，所以我们能只用比如1的值。但是因为我们有一个active panel需要设置在current panel下面，所以我们设置z-index如下：

```css
.panel.current {
	z-index: 2;
}

.panel.active {
	z-index: 1;
}
```

这就能够保证current在所有元素上面，而新加入得元素在其下面。

鉴于我们要使用脚本控制每个带有“bg-img”的元素加入3D转换效果（之前我们提过，我们要用像素作为转换的参数，因此需要window的宽和高），我们任然需要设置一些东西在我们得样式表中，比如过渡效果和新panel的动画。

因此，让我们定义一个transition给我们的transform元素并带有过渡时间和松弛度得参数：

```css
.panel.current .bg-img {
	transition: transform 1.1s ease-in-out;
}
```
对于第一个特效（螺旋效果）和第二个（将瓷片移除到外部），我们想让起动画开始有一个按比例缩减得效果：

```css
.boxgallery[data-effect="effect-1"] .panel.active .bg-tile,
.boxgallery[data-effect="effect-2"] .panel.active .bg-tile {
	animation: scaleDown 1.1s ease-in-out;
}

@keyframes scaleDown {
	from { transform: translate3d(0,0,380px); }
	to { transform: translate3d(0,0,0); }
}
```

我们给每个瓷片的子元素加入了一点动画延迟，然后使用cubic-bezier让所有动画都快一点。这个变动将会使得“小瓷片”上下渐变消失时间不同：

```css
/* Variation 2 */

.boxgallery[data-effect="effect-2"] .panel.current .bg-img {
	transition: transform 0.9s cubic-bezier(0.7,0,0.3,1);
}

.boxgallery[data-effect="effect-2"] .panel.current .bg-tile:nth-child(2) .bg-img {
	transition-delay: 0.15s;
}

.boxgallery[data-effect="effect-2"] .panel.current .bg-tile:nth-child(3) .bg-img {
	transition-delay: 0.3s;
}

.boxgallery[data-effect="effect-2"] .panel.current .bg-tile:nth-child(4) .bg-img {
	transition-delay: 0.45s;
}
```

第三部分在新面板加入时使用一个覆盖层渐出达到模糊效果：


```css
/* Variation 3 */

.boxgallery[data-effect="effect-3"] .panel::after {
	position: absolute;
	width: 100%;
	height: 100%;
	background: rgba(0,0,0,0.8);
	content: '';
	transition: opacity 1.1s ease-in-out;
}

.boxgallery[data-effect="effect-3"] .panel.current::after,
.boxgallery[data-effect="effect-3"] .panel.active::after {
	opacity: 0;
}

.boxgallery[data-effect="effect-3"] .panel.current::after {
	transition: none;
}
```

我们同时也使用cubic-bezier和延迟一些时间：

```css
.boxgallery[data-effect="effect-3"] .panel.current .bg-img {
	transition: transform 1.1s cubic-bezier(0.7,0,0.3,1);
}

.boxgallery[data-effect="effect-3"] .panel.current .bg-tile:nth-child(2) .bg-img {
	transition-delay: 0.15s;
}

.boxgallery[data-effect="effect-3"] .panel.current .bg-tile:nth-child(3) .bg-img {
	transition-delay: 0.3s;
}

.boxgallery[data-effect="effect-3"] .panel.current .bg-tile:nth-child(4) .bg-img {
	transition-delay: 0.45s;
}
```



## JS部分

就像之前提到过的，我们选择使用js来操作动画，目的是使用以像素宽高的动画。当然我们也可以选择在css中使用百分比为的动画，那样会更加方便，但是却使我们的动画抽搐。

首先看看这个定义函数。

我们首先开始定义我们将赋予panels滑动的变换动画。我们需要分别制定左右箭头的变换。让我们定义一个合适的**setTransforms**函数结构。

接下来，我们获取特效类别从**data-effect**属性。这个将指明我们调用文件是实现定义的哪个文件。

然后，我们初始化一些变量，然后创建一些四个盒子必要的DOM结构。对于那个，每个面板的图片被4个分割部分所替代，每个部分都有同样得src。我们同样加入前后箭头`nav`节点。

```js
BoxesFx.prototype._init = function() {
	// set transforms configuration
	this._setTransforms();
	// which effect
	this.effect = this.el.getAttribute( 'data-effect' ) || 'effect-1';
	// check if animating
	this.isAnimating = false;
	// the panels
	this.panels = [].slice.call( this.el.querySelectorAll( '.panel' ) );
	// total number of panels (4 for this demo)
	//this.panelsCount = this.panels.length;
	this.panelsCount = 4;
	// current panel´s index
	this.current = 0;
	classie.add( this.panels[0], 'current' );
	// replace image with 4 divs, each including the image
	var self = this;
	this.panels.forEach( function( panel ) {
		var img = panel.querySelector( 'img' ), imgReplacement = '';
		for( var i = 0; i < self.panelsCount; ++i ) {
			imgReplacement += '<div class="bg-tile"><div class="bg-img"><img src="' + img.src + '" /></div></div>'
		}
		panel.removeChild( img );
		panel.innerHTML = imgReplacement + panel.innerHTML;
	} );
	// add navigation element
	this.nav = document.createElement( 'nav' );
	this.nav.innerHTML = '<span class="prev"><i></i></span><span class="next"><i></i></span>';
	this.el.appendChild( this.nav );
	// initialize events
	this._initEvents();
}

```

动画中变化的变量是使用window得`width`和`height`。我们多加了10像素，这样动画变换就不会在最后可看见的部分结束，并使得其更加顺滑：

```js
BoxesFx.prototype._setTransforms = function() {
	this.transforms = {
		'effect-1' : {
			'next' : [
				'translate3d(0, ' + (win.height/2+10) + 'px, 0)', // transforms for panel 1
				'translate3d(-' + (win.width/2+10) + 'px, 0, 0)', // transforms for panel 2
				'translate3d(' + (win.width/2+10) + 'px, 0, 0)', // transforms for panel 3
				'translate3d(0, -' + (win.height/2+10) + 'px, 0)' // transforms for panel 4
			],
			'prev' : [
				'translate3d(' + (win.width/2+10) + 'px, 0, 0)',
				'translate3d(0, ' + (win.height/2+10) + 'px, 0)',
				'translate3d(0, -' + (win.height/2+10) + 'px, 0)',
				'translate3d(-' + (win.width/2+10) + 'px, 0, 0)'
			]
		},
		'effect-2' : {
			'next' : [
				'translate3d(-' + (win.width/2+10) + 'px, 0, 0)',
				'translate3d(' + (win.width/2+10) + 'px, 0, 0)',
				'translate3d(-' + (win.width/2+10) + 'px, 0, 0)',
				'translate3d(' + (win.width/2+10) + 'px, 0, 0)'
			],
			'prev' : [
				'translate3d(0,-' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,-' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,' + (win.height/2+10) + 'px, 0)'
			]
		},
		'effect-3' : {
			'next' : [
				'translate3d(0,' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,' + (win.height/2+10) + 'px, 0)'
			],
			'prev' : [
				'translate3d(0,-' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,-' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,-' + (win.height/2+10) + 'px, 0)',
				'translate3d(0,-' + (win.height/2+10) + 'px, 0)'
			]
		}
	};	
}
```

接下来要做得就是初始话nav箭头事件，和调整window大小得事件:

```js
BoxesFx.prototype._initEvents = function() {
	var self = this, navctrls = this.nav.children;
	// previous action
	navctrls[0].addEventListener( 'click', function() { self._navigate('prev') } );
	// next action
	navctrls[1].addEventListener( 'click', function() { self._navigate('next') } );
	// window resize
	window.addEventListener( 'resize', function() { self._resizeHandler(); } );
}
```

再让我们来看看navigate函数。


首先，我们得到两者，现在展现的面板和即将浮现的面板。我们当然需要重新设置current变量。

接下来，我们加入active类给现在的（即将浮现的）面板去触发在css中定义过对的动画。我们最后将我们之前定义的3D变换给现在的面板。这将使得我们4个面板变化，并使后面得面板滑动浮现。

```js
BoxesFx.prototype._navigate = function( dir ) {
	if( this.isAnimating ) return false;
	this.isAnimating = true;

	var self = this, currentPanel = this.panels[ this.current ];

	if( dir === 'next' ) {
		this.current = this.current < this.panelsCount - 1 ? this.current + 1 : 0;			
	}
	else {
		this.current = this.current > 0 ? this.current - 1 : this.panelsCount - 1;
	}

	// next panel to be shown
	var nextPanel = this.panels[ this.current ];
	// add class active to the next panel to trigger its animation
	classie.add( nextPanel, 'active' );
	// apply the transforms to the current panel
	this._applyTransforms( currentPanel, dir );

	// let´s track the number of transitions ended per panel
	var cntTransTotal = 0,
		
		// transition end event function
		onEndTransitionFn = function( ev ) {
			if( ev && !classie.has( ev.target, 'bg-img' ) ) return false;

			// return if not all panel transitions ended
			++cntTransTotal;
			if( cntTransTotal < self.panelsCount ) return false;

			if( support.transitions ) {
				this.removeEventListener( transEndEventName, onEndTransitionFn );
			}

			// remove current class from current panel and add it to the next one
			classie.remove( currentPanel, 'current' );
			classie.add( nextPanel, 'current' );
			// reset transforms for the currentPanel
			self._resetTransforms( currentPanel );
			// remove class active
			classie.remove( nextPanel, 'active' );
			self.isAnimating = false;
		};

	if( support.transitions ) {
		currentPanel.addEventListener( transEndEventName, onEndTransitionFn );
	}
	else {
		onEndTransitionFn();
	}
}

BoxesFx.prototype._applyTransforms = function( panel, dir ) {
	var self = this;
	[].slice.call( panel.querySelectorAll( 'div.bg-img' ) ).forEach( function( tile, pos ) {
		tile.style.WebkitTransform = self.transforms[self.effect][dir][pos];
		tile.style.transform = self.transforms[self.effect][dir][pos];
	} );
}

BoxesFx.prototype._resetTransforms = function( panel ) {
	[].slice.call( panel.querySelectorAll( 'div.bg-img' ) ).forEach( function( tile ) {
		tile.style.WebkitTransform = 'none';
		tile.style.transform = 'none';
	} );
}
```





** 本文翻译自[How to Create a Tiled Background Slideshow](http://tympanus.net/codrops/2014/06/11/how-to-create-a-tiled-background-slideshow/),如果有问题请在评论中指出,谢谢 **
























