title: zepto源码学习
subtitle: 对象关系
tags:
- javascript
- 前端
layout: post
categories: JS
date: 2016-04-09
---

今年重新找工作的时候面试官问了个问题，jQuery的链式函数调用的原理。当时一脸懵逼，因为完全没有去了解过。（事实证明面试能够很好的提高自己）

然后感觉直接去看jq的源码，要仔细看好感觉得花很长时间，还是先看看zepto,反正组织代码的原理都差不多。

## 构造

在立即调用函数中声明了3个东东：
``` js
zepto = {};
function Z(dom, selector) {
  var i, len = dom ? dom.length : 0
  for (i = 0; i < len; i++) this[i] = dom[i]
  this.length = len
  this.selector = selector || ''
}
$ = function(selector, context){
  return zepto.init(selector, context)
}
```
当然声明的位置各有不同，Z函数很显然是构造函数，zepto是构造中间的一些方法的保留吧，然后$上挂上所有对外的方法。构造调用的基本流程是，$调用zepto的init，然后init函数做一些处理，再返回一个Z的｀return zepto.Z(dom, selector)｀,其中zepto.Z返回的就是一个构造函数Z。这样做的处理是要在$上吧zepto挂上，可以在插件里面改写。
<!--more-->



## $

高级函数比如`each，extend`等不是对dom元素做操作的都直接`$.xxx`形式组织，而要对dom操作的都是放在`$.fn`当中。

而我们开题说的jq链式操作看看$.fn中随便一个函数就明白了：
``` js
each: function(callback){
      emptyArray.every.call(this, function(el, idx){
        return callback.call(el, idx, el) !== false
      })
      return this
    },
```
每个函数如果可以链式操作的都会返回this，就是因为返回this所以可以继续对其进行操作。（我当时答了个原型链－－）

## 插件原理

``` js
zepto.Z.prototype = Z.prototype = $.fn
``` 

上面的代码把$.fn加到另外两者的原型上。而我们的$构造函数就是返回的Z。所以，可以在$()上调用挂在fn上的方法。

举个例子：
zepto中有个each，他的定义有两处：
``` js
$.each = function(elements, callback){
  var i, key
  if (likeArray(elements)) {
    for (i = 0; i < elements.length; i++)
      if (callback.call(elements[i], i, elements[i]) === false) return elements
  } else {
    for (key in elements)
      if (callback.call(elements[key], key, elements[key]) === false) return elements
  }

  return elements
}
```
下面这个是定义在fn上面的
``` js 
each: function(callback){
    emptyArray.every.call(this, function(el, idx){
      return callback.call(el, idx, el) !== false
    })
    return this
  }
```
意思大概就是：
$.each 不等于 $().each

其中$()是返回一个Z而$.each就是$.each

