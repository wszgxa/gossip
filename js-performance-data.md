title: JS 性能优化 - 数据存取
tags:
- javascript
- 性能
layout: post
categories: JS
date: 2017-11-20
---

## console.time & console.timeEnd

为了比较具体代码快的运行速度我们需要一些度量工具。`console.time`和`console.timeEnd`是浏览器原生就支持的属性。具体用法：
``` js
function generate100Array() {
  var arr = new Array(100);
  for(var i = 0; i < 100; i++){
    arr[i]='';
  }
  return arr
}
var a = generate100Array()
var b = generate100Array()
console.time('for')
for (var i = 0, len = a.length; i < len; i++) {
  a[i] = i
}
console.timeEnd('for')
console.time('forEach')
b.forEach(function (el, index) {
  b[index] = index
})
console.timeEnd('forEach')
```

你可以复制粘贴上面代码到chrome 终端运行一下，你会发现`for`比`forEach`快，在我的chrome62.0 大概是快3-4倍。列出的理论，我都会提供相应代码，大家可以自己去试试。我只会在chrome下实验，并给出比较结果。

<!-- more -->

## 管理作用域

### 执行上下文解析

下面这几个解释有点偏理论，而且也是相对较老的解释了（但并没有错），现在的作用域解析都与词法分析扯上了关系，想专研的同学可以看这个[Executable Code and Execution Contexts](https://www.ecma-international.org/ecma-262/#sec-executable-code-and-execution-contexts)（后面会研究研究总结下）。

**作用域链**：

在JS中每一个函数其实和其他对象一样，拥有可以通过JS访问的属性，也有一系列不可以通过代码访问仅供JS引擎存取的内部属性。其中一个内部属性是`[[scope]]`。而[[scope]]包含了一个函数被创建的作用域对象的集合，这个集合有前后顺序，一般叫为作用域链。作用域链中的每个对象都是可变对象，每个可变对象都以‘键值对’的形式存在。

**执行环境**：

函数的作用域链会在函数执行的时候用到。当函数执行时会创建一个执行环境（execution context）的内部对象。每一次执行函数，创建的执行环境都是不一样的。多次调用创建多次，然后函数执行完也就销毁了。每个执行环境有自己的作用域链，用于解析标识符。执行环境的作用域链会初始化为当前函数`[[scope]]`属性中对象，顺序也一样。

**活动对象**：
当执行环境的作用链初始化完成就会创建一个活动对象（activation object），活动对象包含了所有的局部变量，命名参数，参数集合还有this，然后活动对象会被推入执行作用域的最前端。

**标识符解析**:

好，重点来了。

就像上面说的一样，最后生成了一个执行作用域链，当解析一个标识符的时候就会按照作用域链的顺序向上搜索。

### 标识符解析的性能问题

在js做运算的时候，所有操作都是有性能开销的，标识符解析也是一样。当一个标识符位置越深，它的读写速度就越慢。考虑以下代码：
``` js
function countUiNumFirst () {
    var divAndP = document.getElementsByTagName('div').length + document.getElementsByTagName('p').length
    var aAndSection = document.getElementsByTagName('a').length + document.getElementsByTagName('section').length
    return divAndP + aAndSection;
}
function countUiNumSecond () {
    var doc = document;
    var divAndP = doc.getElementsByTagName('div').length + doc.getElementsByTagName('p').length
    var aAndSection = doc.getElementsByTagName('a').length + doc.getElementsByTagName('section').length
    return divAndP + aAndSection;
}
console.time('first')
countUiNumFirst()
console.timeEnd('first')

console.time('second')
countUiNumSecond()
console.timeEnd('second')
```
你可以将上面的代码复制在chrome 的devtool里的console里面复制执行。我们这里只有4次去调用全局变量和局部变量，作用域嵌套也只有一层。量相对比较小，但是你还是能明显感觉到作用域嵌套更深，性能消耗更大。

从上面你可以看到，我们将document缓存在了函数内后能提升性能，这样你就能理解你同事写的代码里面通常会出现这种结构的原因了：

``` js
(function(win, doc){
  // some code here
})(window, document)
```
一个是简写`window`和`document`，另外一个主要的原因还是在立即执行函数可能会多次调用`window`和`document`，这样简写能提升性能。


除了缓存变量以外，还可以动态改变作用域来提升性能。在JS里面有一些特定的语法能改变作用域，比如`with` `try catch`和`eval`。当然除了`try catch`我们通常用来捕捉错误以外，其他两个我们一般都不推荐用。自行百度哈。
