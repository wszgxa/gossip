title: 使用伪元素创建图片堆叠特效
subtitle: 日常翻译四
tags: 
- HTML5
- CSS3
- 伪元素
- 翻译
layout: post
categories: HTML5
date: 2015-08-19
---

今天我们将尝试去将在html中单独的一张图片利用css变换出堆叠的效果。关键的地方就是伪元素。

同时我们将看到，在我们实现的过程中，我们将会很快遇到一些混乱的代码，然后我们将使用一些方法使得代码变得整洁。

<!--more-->

![](http://7fvhwe.com1.z0.glb.clouddn.com/stackscss-0.jpg)


## 挑战！

我在找一些素材给我的下载站的时候，我发现了一个小巧且免费的PSD名字叫做(Stack)[http://pixelsdaily.com/resources/photoshop/psds/stacks/],他能够帮助你快速且容易的构建一个偏光图片就像在ps里一样。

![](http://7fvhwe.com1.z0.glb.clouddn.com/stackscss-1.jpg)

就如你所见，它相当的不错。然后呢，我突然就想尝试用css来制作这种效果。这是一种比较复杂的情况，因为这里有很多层堆叠在一起，然而我又不像杂乱的将3张图片叠在一起。因此我们能够最小化的使用html标记，并达到这种效果。最后发现，结果就是使用伪元素。

## HTML

我们仅仅需要一个div来包含一个img，从而完成我们的第一层堆叠效果。然后html就够了，剩下的全是css的活。

```html
<div class="stackone">
   <img src="http://lorempixum.com/200/200/city/7">
</div>
```

## 基础样式

基础样式没有完成特别的内容，只是设置了基础的,margin和padding这样在我们的浏览器中就能够呈现一样的内容了。还有就是我们的图片的border将会时白色，因此我们需要给背景一点颜色。


```css
* {margin: 0; padding: 0;}
body {background: #ccd3d7;}
```

## 顶部图片样式

现在是时候让我们无聊的图片变得有趣点。这里有一大块的代码：

```css
.stackone {
    border: 6px solid #fff;
    float: left;
    height: 200px; width: 200px;
    margin: 50px;
    position: relative;
    -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```

让我们把他们分成一部分一部分的讲解下。这里4行代码决定了我们图片的大小和位置：

```css
float: left;
height: 200px; width: 200px;
margin: 50px;
position: relative;
```

因为我们需要一个画廊的效果，我们将照片浮动到左边然后设置一个适当的50px的margin让让他们隔开点。高和宽我们都设置成我们照片的宽高（200px by 200px）。

这段代码最有趣的地方是，我们将其设置城相对定位。就像现在看到的，它并不会做出特别的效果。我这样做的原因是，我知道我后面的伪元素将要使用绝对定位。我想让他们的位置都相对于元素定位，而不是页面。


### 酷...

剩下的代码部分主要时为了美观。我加入了一个较厚的白色border和一个盒阴影，最好加上对应的前缀使得其有较好的兼容性。

```css
border: 6px solid #fff;
-webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
-moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
```

### 运行看看

可以再结果中看到我们的顶部照片看起来非常不错。而我们才加入几行代码而已。

![](http://7fvhwe.com1.z0.glb.clouddn.com/选区_002.png)


## 第一个伪元素

现在是时间加入我们的第一个堆叠效果了。基本上我们只是想让其看起来像是其堆叠在另外一张照片上面。我们将使用:before伪元素来达到这个效果，并使用和上面差不多的代码。

为了达到效果，我们将使用下面这种语法：

```css
.stackone:before {
    content: "";
}
```

上面这段代码，我们定位到了我们的第一张图片，然后加上了没有内容的:before伪元素。这样我们就得到了一个可以加入特效的元素了。现在，让我们给他添加和便面图片相同的样式：

```css
.stackone:before {
    content: "";
    height: 200px; width: 200px;
    background: #eff4de;
    border: 6px solid #fff;
    -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```

这里主要需要理解的是，我们并没有和图片打交道。我能够加载另外一张图片，但是这是一个坏主意，你可以想想我将重复加载一张图片三次。幸运的是，伪元素的大部分将会被遮挡，我们能够填充一个固定的颜色然后任然能达到我们想要的效果。

### 定位

现在，我们运行看结果，看起来并不如意。我们搞乱了我们的图片。

![](http://7fvhwe.com1.z0.glb.clouddn.com/stackscss-3.jpg)

就像你看见的，我们的伪元素干扰了我们的图片的布局。为了修复这个，我们需要加上定位的代码，然后用z-index将它定在image后面。我将不同部分的嗲吗分开了，这样你就能清除看清我们每个部分都是干嘛的了。

```css
.stackone:before {
    content: "";
    height: 200px; width: 200px;
    background: #eff4de;
    border: 6px solid #fff;
     
    position: absolute;
    z-index: -1;
    top: 0px;
    left: -10px;
     
    -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
     
    -webkit-transform: rotate(-5deg);
    -moz-transform: rotate(-5deg);
    -o-transform: rotate(-5deg);
    -ms-transform: rotate(-5deg);
    transform: rotate(-5deg);
}
```

通过使用绝对定位，我们能够将我们的图片“推”到我们图片的起始位置。就像我前面说的z-index将这个伪元素变成了背景。最后我们使用transform特效让图片旋转一个角度，这样就得到了你在原来psd上看到的效果。

### 运行看看

现在，一切看起来都不错！我们的“第二张照片”很好的叠在我们第一张图片下面，然后其还有一个很好的角度，看起来就和原第一张图片时分割开的。

![](http://7fvhwe.com1.z0.glb.clouddn.com/选区_003.png)

## 第二个伪元素

就像你能猜到的那样，去为了加上第三个图片堆叠的效果，我们需要使用:after伪元素。这个工作和刚刚做的差不多，我们没有必要细致的再讲一遍。我们只需要改变位置和转移角度即可。

```css
.stackone:after {
    content: "";
    height: 200px; width: 200px;
    background: #768590;
    border: 6px solid #fff;
    position: absolute;
    z-index: -1;
    top: 5px;
    left: 0px;
    -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -webkit-transform: rotate(4deg);
    -moz-transform: rotate(4deg);
    -o-transform: rotate(4deg);
    -ms-transform: rotate(4deg);
    transform: rotate(4deg);
}
```

## 运行看看

看到了吗？我们制作好了我们第一个图片堆叠效果。只需要一张图片，就可以这么漂亮。

![](http://7fvhwe.com1.z0.glb.clouddn.com/选区_004.png)

## 有缺陷的工作流程

给出的psd有几种不同的图片堆叠效果，我也想使用css达到同样的效果。如果我们只是将其复制粘贴然后改变位置和角度，这不是又是另外一个坑吗？

问题是这样的：想上面那样做了3个堆叠特效，会有一大堆可怕的夯余代码。仔细看看，你会发现其比1977年的表格布局还要恶心。我们做了一大堆的重复工作。

### “DRY”CSS

Andy Hunt和Dave Thomas 在编程中创造了“DRY”术语，它的意思时不要做重复的事。然而，CSS不是科学上的编程语言，它只是一个样式编辑语言。然而，如果我们把它看做编程语言，并利用DRY模式去对待它，我们将会得到更好的代码。

## 再试试！

如果我们创建一大堆的classes选择器，我们的CSS将会变得十分笨重。我们将不得不创建一个又一个单独的class然后贴上一大堆的代码。再加上伪元素的选取之类的，我们又将增加更多重复的代码。

```css
.stackone {...}
.stacktwo {...}
.stackthree {...}
.stackfour {...}
.stackfive {...}
.stacksix {...}
.stackseven {...}
.stackeight {...}
```

这里其实可以将公有的属性用在一起：

```css
.stackone, .stacktwo, .stackthree, .stackfour, .stackfive, .stacksix, .stackseven, .stackeight {
    border: 6px solid #fff;
    float: left;
    height: 200px; width: 200px;
    position: relative;
    margin: 50px;
    -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```

这样要好很多。

注意到我们的这些类都以5个相同字母开始的没有？利用这一点，我们可以使用属性模式匹配选择器，我们只需要指出开始的几个字母就可以了。

```css
div[class*='stack'] {
    border: 6px solid #fff;
    float: left;
    height: 200px; width: 200px;
    position: relative;
    margin: 50px;
    -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
```

这样多好，这样就选择了所有我们要指定的classes。而且，当我们再加入class时，他还是能够选择到。

让我们好好利用这个属性来优化我们的代码。如下代码，你可以看见下面的代码我们去尝试匹配元素的键名只使用了一次。就如你看见的，定义了只有stack的类。然后我们将之引用到:before 和 :after和stack类三者相同的描述中。将上面的范围变窄，我们接着只申明了:before 和 :after的样式。最后我们给:before添加了不同的背景颜色。

```css
div[class*='stack'] {
    float: left;
    position: relative;
    margin: 50px;
}
 
div[class*='stack'], div[class*='stack']:before, div[class*='stack']:after {
    border: 6px solid #fff;
    height: 200px; width: 200px;
    -webkit-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    -moz-box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    box-shadow: 2px 2px 5px rgba(0,0,0,0.3);
}
 
div[class*='stack']:before, div[class*='stack']:after {
    background: #768590;
    content: "";
    position: absolute;
    z-index: -1;
}
 
div[class*='stack']:before {
    background: #eff4de;
}
``` 

我们在这只是字面上的将上百行的代码压缩到一小块代码中。这将有助于代码的维护，减少加载时间，和让你自己保持清醒。最开始使用DRY原则会有一点麻烦，但是你一旦这样做了就不会在回头了。

### 其他的堆叠

![](http://7fvhwe.com1.z0.glb.clouddn.com/stackscss-6.jpg)

这一步中，我们将实现堆叠效果不同代码部分。一点Sass或则Less将会帮助很大，但是读者有些还不熟悉sass或则less所有这部分代码还是css。

```css
/*STACK ONE*/
.stackone:before {
    top: 4px;
    left: -6px;
}
 
.stackone:after {
    top: -2px;
    left: -6px;
}
 
/*STACK TWO*/
.stacktwo:before {
    top: 0px;
    left: -10px;
    -webkit-transform: rotate(-5deg);
    -moz-transform: rotate(-5deg);
    -o-transform: rotate(-5deg);
    -ms-transform: rotate(-5deg);
    transform: rotate(-5deg);
}
 
.stacktwo:after {
    top: 5px; left: 0px;
    -webkit-transform: rotate(4deg);
    -moz-transform: rotate(4deg);
    -o-transform: rotate(4deg);
    -ms-transform: rotate(4deg);
    transform: rotate(4deg);
}
 
/*STACK THREE*/
.stackthree:before {
    top: 5px;
    left: -15px;
    z-index: -1;
    -webkit-transform: rotate(-10deg);
    -moz-transform: rotate(-10deg);
    -o-transform: rotate(-10deg);
    -ms-transform: rotate(-10deg);
    transform: rotate(-10deg);
}
 
.stackthree:after {
    top: -2px;
    left: -10px;
    -webkit-transform: rotate(-5deg);
    -moz-transform: rotate(-5deg);
    -o-transform: rotate(-5deg);
    -ms-transform: rotate(-5deg);
    transform: rotate(-5deg);
}
```

## 浏览器通用性

在我的测试中，浏览器兼容还是不错的。上面所有的将会在Safari、Firefox、Chrome、Opera和IE9+浏览器表现良好。对于IE7，你将得到一个简单的画框，不会有堆叠特效，在美学上这样显示也还可以。

IE8就比较复杂了，因为它部分支持了上述新特性，使得它让所有变现都扭曲了。你能够决定怎样去解决这个问题。不过我这里使用一个不友好的hack方法去只定位IE8。使用这个方法，我简单的隐藏了:before和:after元素，这样IE8就得到了一个想IE7一样的效果。

```css
div[class*='stack']:before, div[class*='stack']:after {
    background: #768590;
    content: "";
    position: absolute;
    z-index: -1;
     
    /*Dirty IE8 hack*/
    height: 0px\9; width: 0px\9; 
    border: none\9;
}
```

这类选择器在IE8中不被支持，但是带冒号的伪元素选择器是被支持的。如果列出哪些类，而不是使用类名匹配选择器，IE8将会表现出我们之前的特效。

## 总结

我们学了很多今天。其中包括图片堆叠特效，学习如何使用:before和:after，还讨论了使用DRY思想的代码最佳实践。
然后博主打了个广告：[PixelsDaily](http://pixelsdaily.com/)

** 本文翻译自[Use Pseudo Elements to Create an Image Stack Illusion](http://designshack.net/articles/css/use-pseudo-elements-to-create-an-image-stack-illusion/),如果有问题请在评论中指出,谢谢 **

