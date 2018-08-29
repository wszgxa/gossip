title: JS 性能优化 - 脚本的加载和执行
tags:
- javascript
- 性能
layout: post
categories: JS
date: 2017-11-19
---


## 引子

开年的一次面试中记录下来的知识欠缺点，发现这块的东西自己也只是零零散散的，不够系统。面试真的是查漏补缺的好地方，所以就买了一波性能相关的书籍，慢慢看，慢慢总结吧。这一波是记录一些纯js相关的性能提升，不涉及具体框架，纯纯的在写js中需要注意的性能优化点。这部分相关的笔记，大部分内容来自阅读《高性能JavaScript》。

## JS 的加载和执行

基本上这是一个常识，js的加载和执行会阻塞页面的渲染和交互。很多浏览器甚至是用单一进程来处理的UI刷新和JS执行。所以我们在让页面加载的过程中需要注意避免让js的加载执行和浏览器的UI渲染冲突。下面就是一些解决方案：

### 脚本位置

浏览器在处理html的时候，是从上到下解析的。所以，在HTML上的标签位置决定了浏览器解析的先后顺序。为了解决JS与浏览器冲突，通常情况下，我们是将JS放在最后的：

``` html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  </head>
<body>
  <!-- 其他的html -->
  <script src="http://hiluluke.cn/js/simple_build.js"></script>  <!-- 将script放在最后，body前 -->
</body>
</html>
```

<!-- more -->

### 无阻塞加载

除了上面通过脚本位置来时脚本加载和UI渲染不相互阻塞以外，还有下面一些方法可以来达到JS的无阻赛加载和执行。

* 定义defer属性
* 动态创建script
* ajax 注入

#### 设置defer属性
HTML 4 为script标签新增加了一个扩展属性：defer。
当你给你的JS脚本设置了defer属性后，浏览器会在见到defer属性的script就开始下载，但是会等到HTML解析完之后才会执行这段JavaScript。在[caniuse](https://caniuse.com/#search=defer)显示基本上现代流行的浏览器也都支持这个属性。所以可以在实际项目中使用。例子：

``` html
   <script type="text/javascript" src="hiluluke.cn/index.js" defer></script>
```
另外，根据w3c的标准，defer属性只有当设置了src才能生效，所以不能用来改变行内scritp的顺序。


#### 动态创建script

其实很简单一个东西，看段代码就懂了：

``` js
var script = document.createElement('script')
script.type = "text/javascript"
script.onload = function () {
  alert("script loaded")
}
script.src = "file.js"
document.head.appendChild(script)
```
如果当你有代码依赖于`file.js`，你就可以将代码放在onload之后在执行。不过onload在IE9之后才支持，在IE8以及更老要用啊`onreadystatechage`。你可以把这段封装成一个函数：
``` js
function loadScript(url, callback) {
  var script = document.createElement("script")
  script.type = "text/javascript";
  if (script.readyState) { //IE
    script.onreadystatechange = function () {
      if (script.readyState == "loaded" || script.readyState == "complete") {
        script.onreadystatechange = null;
        callback();
      }
    };
  } else { //Others
    script.onload = function () {
      callback();
    };
  }
  script.src = url;
  document.getElementsByTagName_r("head")[0].appendChild(script);
}
loadScript('file1.js', function () {
  loadScript('file2.js', function () {
    alert('load file1 file2')
  })
})
```
当动态加载脚本时，无论在何时启动加载，该文件的下载和执行都不会阻塞页面的其他进程。

#### ajax注入
其实就是将ajax的返回的内容当做脚本生成script标签。当内容返回，脚本注入后，插入的代码就会立即执行。


#### 无阻塞总结

其实就是想办法让JS在页面渲染之后再执行。通常意义上，在页面初始渲染过程中其实并不需要所有的javascript执行。用户在访问一个网页的最初阶段只是想要看见展现的内容，而JS提供的交互在用户现看完内容之后才需要的。所以最好的方式是先加载部分精简的代码，然后再在那部分精简的代码里动态加载需要的js：

``` html
<script type="text/javascript" src="load.js"></script>
<script>
  loadScript('file.js', function() {
    Application.init()
  })
</script>
```
高性能JS中推荐了 [lazyload](https://github.com/rgrove/lazyload)和[LABjs](https://github.com/getify/LABjs)，不过还是具体项目具体分析需要采用什么样的工具。

### 提升脚本加载速度
除了上面提高的避免阻塞UI的优化之外，提升脚本加载速度也是性能提升的点，不过这个其实更于网络相关的性能优化相关，后面再总结，先列一些点：
* 合并脚本
* 缓存脚本
* 使用cdn
* 预加载