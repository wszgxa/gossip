title: zepto源码学习
subtitle: 插件方法
tags:
- javascript
- 前端
layout: post
categories: JS
date: 2016-04-11
---

## $.extend

* 用途 
  扩展对象属性。
  第一个参数如果是true就递归的将source给target。
  ``` js
  $.extend(target, [source, [source2, ...]])  ⇒ target
  $.extend(true, target, [source, ...])  ⇒ target v1.0+
  ```
* 相关代码
  ``` js
  function extend(target, source, deep) {
    for (key in source)
      if (deep && (isPlainObject(source[key]) || isArray(source[key]))) {
        if (isPlainObject(source[key]) && !isPlainObject(target[key]))
          target[key] = {}
        if (isArray(source[key]) && !isArray(target[key]))
          target[key] = []
        extend(target[key], source[key], deep)
      }
      else if (source[key] !== undefined) target[key] = source[key]
  }

  // Copy all but undefined properties from one or more
  // objects to the `target` object.
  $.extend = function(target){
    var deep, args = slice.call(arguments, 1)
    if (typeof target == 'boolean') {
      deep = target
      target = args.shift()
    }
    args.forEach(function(arg){ extend(target, arg, deep) })
    return target
  }
  ```

<!-- more -->

## zepto.qsa


* 用途
  Css 选择器，主要是用`document.querySelectAll`，另外正对一些特殊的css选择做了优化比如"#id"。
  他是一个内部方法，但是通过$.zepto暴露了出来，可以在插件中改写他。

* 代码
  ``` js
    zepto.qsa = function(element, selector){
      var found,
          maybeID = selector[0] == '#',
          maybeClass = !maybeID && selector[0] == '.',
          nameOnly = maybeID || maybeClass ? selector.slice(1) : selector, // Ensure that a 1 char tag name still gets checked
          isSimple = simpleSelectorRE.test(nameOnly)
      return (element.getElementById && isSimple && maybeID) ? // Safari DocumentFragment doesn't have getElementById
        ( (found = element.getElementById(nameOnly)) ? [found] : [] ) :
        (element.nodeType !== 1 && element.nodeType !== 9 && element.nodeType !== 11) ? [] :
        slice.call(
          isSimple && !maybeID && element.getElementsByClassName ? // DocumentFragment doesn't have getElementsByClassName/TagName
            maybeClass ? element.getElementsByClassName(nameOnly) : // If it's simple, it could be a class
            element.getElementsByTagName(selector) : // Or a tag
            element.querySelectorAll(selector) // Or it's not simple, and we need to query all
        )
    }
  ```
  上面有判断是不是含有id选择器，并用不同的选择方式，这样做主要是提高性能。上面的`simpleSelectorRE = /^[\w-]*$/`,用来看是否是在没有子css选择器的简单选择器。return里面有多层判断用来，选择最好的选择器。

## $.fn.concat（带fn的都是绑定在fn上的）

  * 用途
    这个方法只有zepto有，jq没有。把把后面的参数加在第一个参数数组里面。数组和类数组一样的都会一样的操作。
    ``` js
    concat(nodes, [node2, ...])  ⇒ self
    ```
  * 代码
    ``` js
    concat: function(){
      var i, value, args = []
      for (i = 0; i < arguments.length; i++) {
        value = arguments[i]
        args[i] = zepto.isZ(value) ? value.toArray() : value
      }
      return concat.apply(zepto.isZ(this) ? this.toArray() : this, args)
    }
    ```
    上面有判断调用对象是不是zepto对象，是的话就把他的dom元素取出来放数组里面。其中concat.apply中的concat是`concat = emptyArray.concat`空数组的原生方法。

## $.map 

* 用途
  循环遍历传入元素，回调函数判断一波，然后返回正确的数组。
*代码  
  ``` js
  $.map = function(elements, callback){
    var value, values = [], i, key
    if (likeArray(elements))
      for (i = 0; i < elements.length; i++) {
        value = callback(elements[i], i)
        if (value != null) values.push(value)
      }
    else
      for (key in elements) {
        value = callback(elements[key], key)
        if (value != null) values.push(value)
      }
    return flatten(values)
  }
  ```
  其中有个flatten,他是一个私有方法，主要作用就是将数组中的zepto对象元素的dom元素取出来放数组里面。

## $.each $.fn.each (在fn和$上都有这个方法，用法不一样)
* 用途
  遍历元素，这里可以遍历一个数组或者就是zepto对象，当回调函数返回`false`的时候停止遍历。
  两种用法：
  ``` js
  $('form input').each(function(index){
    console.log('input %d is: %o', index, this);
  })
  $.each([1,2,3], function(index){
    console.log('input %d is: %o', index, this);
  })
  ```
* 代码
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
  当是一个类数组调用时用$上的方法，当是zepto对象调用的时候用fn上的方法。

