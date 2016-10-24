title: canvas 笔记（1）
tags:
- javascript
- canvas
layout: post
categories: JS
date: 2015-10-25
---

## 初步

首先需要在html中定义html结构
```html
<canvas id="mycanvas" width="600" height="300"></canvas>
```
然后需要在js上获取canvas和定义动画类型

```js
var canvas = document.getElementById("mycanvas"); //获取canvas
var context = canvas.getContext("2d");//定义2d动画
```

然后就可以进行一些绘图操作：

```js
context.lineWidth = 10;定义线条宽度
context.strokeStyle = "blue";//定义线条样式（颜色）
context.moveTo(50, canvas.height)；移动光标
contex.lineTo(x,y)//从光标的位置画一条线到(x,y)
context.stroke()绘图
```

<!--more-->


## 线条

### 画弧

* context.arc(a, b, c, d, e, f);

第一个参数，第二个参数是圆心坐标，第三个参数是开始弧度，第四个是结束弧度。最后一个参数是方向False = 顺时针，true = 逆时针。

* context.arcTo(x1, y1, x2, y2, radius);

(x1,y1)弧的起点 （x2，y2）弧的终点 radius半径

### 画二次曲线

* context.quadraticCurveTo(controlX, controlY, endingPointX, endingPointY);

![](http://7fvhwe.com1.z0.glb.clouddn.com/选区_024.png)

其中context point是开始的光标位置。其中有4个相切。midpoint1,2上各有两个正切


### 画贝塞尔曲线

* context.bezierCurveTo(controlPointX1, controlPointY1,controlPointX2, controlPointY2, endingPointX, endingPointY);

!()[http://7fvhwe.com1.z0.glb.clouddn.com/选区_025.png]

### 线段端点

* context.linejoin=x;线交点样式
其中x有3个选线：bevel（斜角）、round（圆角）、miter（默认尖角）

* context.linegap=x;线的端点样式
其中x有3个参数：butt（默认，平直边缘）、round(圆形线帽)、square（正方形线帽）

## 文字

context.font = "40pt Calibri";设置字体大小
context.fillText("Hello World!", canvas.width / 2, 120);绘文字
context.strokeText("Hello World!", x, y);等价于上面


## 线条demo：分型树

```js
function drawBranches(context, startX, startY, trunkWidth, level) {
  if (level < 12) {
    var changeX = 100 / (level + 1);
    var changeY = 200 / (level + 1);
    var topRightX = startX + Math.random() * changeX+10;
    var topRightY = startY - Math.random() * changeY;
    var topLeftX = startX - Math.random() * changeX-10;
    var topLeftY = startY - Math.random() * changeY;
    // draw right branch
    context.beginPath();
    context.moveTo(startX + trunkWidth / 4, startY);
    context.quadraticCurveTo(startX + trunkWidth / 4, startY - trunkWidth, topRightX, topRightY);
    context.lineWidth = trunkWidth;
    context.lineCap = "round";
    context.stroke();
    // draw left branch
    context.beginPath();
    context.moveTo(startX - trunkWidth / 4, startY);
    context.quadraticCurveTo(startX - trunkWidth / 4, startY - trunkWidth, topLeftX, topLeftY);
    context.lineWidth = trunkWidth;
    context.stroke();
    drawBranches(context, topRightX, topRightY, trunkWidth * 0.7, level + 1);
    drawBranches(context, topLeftX, topLeftY, trunkWidth * 0.7, level + 1);
  }
}
```

## 形状

### 矩形

```js
context.rect(x,y,width,height);
context.fillStyle = "#8ED6FF";
context.fill();
context.lineWidth = 5;
context.strokeStyle = "black";
```
上述方法绘制一个带边框的矩形。(x,y)为开始绘制的左上定点，width和height为宽高

```js
context.fillRect(x,y,width,height);
context.strokeRect(x,y,width,height);
```

上面为绘制填充矩形和描边矩形，就一个没边，一个没填充

### 画一个圆

之前画弧的arc

```js
context.arc(canvas.width / 2, canvas.height / 2, 70, 0, 2 *Math.PI, false);
context.fillStyle = "#8ED6FF";
context.fill();
context.lineWidth = 5;
context.strokeStyle = "black";
```

### 自定义形状和样式

* context.closePath();闭合路径
* context.fillStyle = fillStyle;其中fillStyle可以是一个颜色，还可以是一个图片也可以是一个渐变对象：
     grd = context.createLinearGradient(canvas.width * 2 / 5, triangleY, canvas.width * 2 / 5, triangleY + triangleHeight);
     grd.addColorStop(0, "#8ED6FF"); // light blue
     grd.addColorStop(1, "#004CB3"); // dark blue
grd = context.createLinearGradient(canvas.width * 2 / 5, triangleY, canvas.width * 2 / 5, triangleY + triangleHeight);
线性渐变上面4个参数分别是渐变开始点的(x,y)坐标和结束点(x,y)坐标

然后指定渐变颜色是：
grd.addColorStop(0, "#8ED6FF"); // light blue
grd.addColorStop(1, "#004CB3"); // dark blue
第一个参数是百分比位置，第二个参数是渐变颜色值

* grd = context.createRadialGradient(centerX, centerY, 10, centerX, centerY, 100);

径向渐变和线性渐变差不多,其中第三个参数和第六个参数分别是开始圆的半径和结束圆的半径

* var pattern = context.createPattern(image,"repeat|repeat-x|repeat-y|no-repeat");

第一个参数可以是image或者video或者画布，第二个参数指定重复。
他是指定为fillStyle的。可以用来填充图像、视频、画布。


### 画透明的形状

context.globalAlpha = 0.5;
设置全局透明度。

### save和restore
context.save();保存之前的样式设置
context.restore();恢复之前的样式设置

### 图形混合运算

* context.globalCompositeOperation = x;
其中x可以是：source-atop / source-in / source-out / source-over / destination-atop / destionation-in / destionation-out / destination-over / lighter / xor / copy

![](http://7fvhwe.com1.z0.glb.clouddn.com/选区_026.png)

上面的图都是在相同的位置画一个正方形和一个圆，然后用了不同的globalCompositeOperation得到的不同效果。

注意是先画的正方形后画的圆形


## 图片和视频

### 画一张图

* context.drawImage(this,x,y)
其中this是图片对象，而(x,y)是坐标。一般将上面代码放在image加载回调里面。

* 裁剪图片

Context.drawImage(imageObj, sourceX, sourceY, sourceWidth, sourceHight,
destX, destY, destWidth, destHeight);看下面这张图应该就能懂了

![](http://7fvhwe.com1.z0.glb.clouddn.com/选区_028.png)

* 画视频

```js
<script>
  window.requestAnimFrame = (function(callback){
    return window.requestAnimationFrame ||
    window.webkitRequestAnimationFrame ||
    window.mozRequestAnimationFrame ||
    window.oRequestAnimationFrame ||
    window.msRequestAnimationFrame ||
    function(callback){
      window.setTimeout(callback, 1000 / 60);
    };
  })();

  function drawFrame(context, video){
    context.drawImage(video, 0, 0);
    requestAnimFrame(function(){
      drawFrame(context, video);
    });
  }

  window.onload = function(){
    var canvas = document.getElementById("myCanvas");
    var context = canvas.getContext("2d");
    var video = document.getElementById("myVideo");
    drawFrame(context, video);
  };
</script>
</head>
<body>
  <video id="myVideo" autoplay="true" loop="true" style="display:none;">
  <source src="http://www.html5canvastutorials.com/cookbook/ch3/1369_03_03/BigBuckBunny_640x360.ogv" type="video/ogg"><source src="http://www.html5canvastutorials.com/cookbook/ch3/1369_03_03/BigBuckBunny_640x360.mp4" type="video/mp4">
</video>
  <canvas id="myCanvas" width="600" height="360" style="border:1px solid black;">
</canvas>
```
主要思想：将video隐藏，然后一帧一帧抓video呈现在canvas


* 获取图片数据

```js
var imagedata = context.getImageData(sourceX,sourceY,sourceWidth,sourceHeight)
```

参数分别是起点坐标和宽高。
获取已经画好的image的数据，其返回一个对象。
imagedata.data.length大小数据,其中data表示多少个像素
imagedata.width宽度
imagedata.height高度数据

* 像素反色

context.putImageData(imageData, destX, destY);
将image数据放进固定位置

反色代码：

```js
context.drawImage(this, destX, destY);
var imageData = context.getImageData(sourceX, sourceY,
sourceWidth, sourceHeight);
var data = imageData.data;
for (var i = 0; i < data.length; i += 4) {
  data[i] = 255 - data[i]; // red
  data[i + 1] = 255 - data[i + 1]; // green
  data[i + 2] = 255 - data[i + 2]; // blue
  // i+3 is alpha (the fourth element)
}
  // overwrite original image with
  // new image data
  context.putImageData(imageData, destX, destY);
};
  imageObj.src = "jet_300x214.jpg";
```

* 转变图片灰度

```js
for (var i = 0; i < data.length; i += 4) {
  var brightness = 0.34 * data[i] + 0.5 * data[i + 1] + 0.16 * data[i + 2];
  data[i] = brightness; // red
  data[i + 1] = brightness; // green
  data[i + 2] = brightness; // blue
  // i+3 is alpha (the fourth element)
}
```
* 将一个canvas画布转换成data URL
可以使用local storge将data url数据存储到本地


var dataURL = canvas.toDataURL();


其中返回的dataURL就是data URL
将datauri指定在src中就可以载入图片。
