title: zepto源码学习
subtitle: 一些私有函数和方法
tags:
- javascript
- 前端
layout: post
categories: JS
date: 2016-04-07
---

在Zepto里面有一些没有暴露出来的私有方法，有一些值得借鉴的地方，比如里面用来判断类型的type函数。大概纪录如下：

### 一些类型判断方法


判断数组：
``` js
isArray = Array.isArray ||
      function(object){ return object instanceof Array }
```

*类型判断函数*
``` js
var class2type = {},
    toString = class2type.toString;
// class2type map
$.each("Boolean Number String Function Array Date RegExp Object Error".split(" "), function(i, name) {
  class2type[ "[object " + name + "]" ] = name.toLowerCase()
});

function type(obj) {
    return obj == null ? String(obj) :
      class2type[toString.call(obj)] || "object"
  }
```
上面的类型判断，基本上可以判断所有的类型。主要是改变toString的this指向来实现的。

<!-- more -->



是否是函数，对象(用了上面的type方法)：
``` js
function isFunction(value) { return type(value) == "function" }
function isObject(obj)     { return type(obj) == "object" }
```


判断Window,Document节点
``` js
  function isWindow(obj)     { return obj != null && obj == obj.window }
  function isDocument(obj)   { return obj != null && obj.nodeType == obj.DOCUMENT_NODE }
```


判断是否是 plainObject(就是通过｛｝和new Object声明出来的空对象吧)
``` js
function isPlainObject(obj) {
    return isObject(obj) && !isWindow(obj) && Object.getPrototypeOf(obj) == Object.prototype
  }
```


判断是是不是`类数组`
``` js
function likeArray(obj) { return typeof obj.length == 'number' }
```

### 数组方法及利用

提取了3个数组的方法`concat`,`filter`,`slice`

``` js
var emptyArray = [], concat = emptyArray.concat, filter = emptyArray.filter, slice = emptyArray.slice;
```


用filter来过滤数组中的空项：

``` js
  function compact(array) { return filter.call(array, function(item){ return item != null }) }
```


删除数组中重复的元素 *

``` js
  uniq = function(array){ return filter.call(array, function(item, idx){ return array.indexOf(item) == idx }) }
```

