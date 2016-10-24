title: 自执行匿名函数及其与闭包的关系
date: 2015-02-21 22:54:11
tags:
- javascript
- 闭包
layout: post
categories: JS
---

对这个感兴趣是因为看[javascript 秘密花园](http://bonsaiden.github.io/JavaScript-Garden/zh/)看到闭包部分，讲到匿名包装器（自执行匿名函数）时产生了好奇，虽然之前也有接触，但是只是知道这么个东西。今天查了些资料，现在总结下，权当笔记。

## 函数的三种定义方式
* 第一种

```js
function addNum(x,y){
  return x+y;
}
```
<!--more-->
* 第二种

```js
var addNum = new Function('x,y','return x+y');
```
* 第三种

```js
var addNum = function(x){return x+y;}
```

上面三种第一种是最常规的，调用语句可以在其之前也可以在其之后调用。第二种，大家都说不要这么用，就不要用咯。第三种是将匿名函数赋值给变量addNum，调用语句只能在其后面。

## 匿名函数的创建

* 第一种：就是上面第三种方式。
* 第二种：

```js
(function(x,y){
  alert(x+y);
  })(3,1);
  ```

  这里创建了一个匿名函数就是第一个圆括号包括的，第二个圆括号调用前面的匿名函数并传入参数。

## 自己执行的匿名函数

自执行匿名函数就是这样的：`(functin{//code})();`就是上面的第二种。

### 自执行的原因

首先，这样的函数是不会自执行的：`function{//code}();`这里是会报错的。原因是因为function在预编译阶段就会被解释，而后面的()去执行就没有执行的东西。可以理解成`function{//code}`和`()`是在两个不同的阶段执行，分别是预编译阶段和编译阶段。

而`(function{//code})()`中第一个括号会将匿名函数转换成表达式，就不会被预编译了。就能在编译阶段立即执行了。

当然将函数转换成表达式的不只是()，还有void和～等。所以立即调用函数有下面几种：

```js
( function() {}() );
( function() {} )();
[ function() {}() ];
~ function() {}();
! function() {}();
+ function() {}();
- function() {}();
delete function() {}();
typeof function() {}();
void function() {}();
new function() {}();
new function() {};
var f = function() {}();
1, function() {}();
1 ^ function() {}();
1 > function() {}();
  ```

  呵呵，javascript就是这样的。。。

## 匿名函数和闭包的关系
闭包其实就是函数的嵌套（或则类的嵌套），内层能使用外层所有变量，而外层不能使用内层变量。
### 闭包使用匿名函数
和匿名函数有啥关系呢？咱看代码。

```js
(function(){
  var foo = 'Hello';
  var bar = 'World!'

  function baz(){
      return foo + ' ' + bar;
  }
  function foo(){
      return foo + ' ' + bar;
  }

  window.baz = baz; //Assign 'baz' to the global variable 'baz'...
})();

console.log(baz()); //...and now this works.
console.log(foo()); //... do not works.
```

这段代码就说明了。其中foo不会执行而baz会执行。主要就是baz赋值给了window这样就暴露在了全局作用域，而foo没有则只能在匿名函数内部调用。

```js
var outer = null;

(function(){
    var one = 1;
    function inner (){
        one += 1;
        alert(one);
    }
    outer = inner;
})();

outer();    //2
outer();    //3
outer();    //4
```

和上面意思差不多只不过不适赋值给window而是一个外部的变量，则在外部作用域暴露了，就能调用。
这和一般的闭包自己比较吧。

### 在for循环中的使用

```js
for(var i = 0; i < 10; i++) {
    setTimeout(function() {
        console.log(i);  
    }, 1000);
}
```

上面的这段代码是不会输出1,2,3,4,5...的只会打印出10个10，因为for循环早于setTimeout执行完毕，匿名函数保持对外部变量i的引用，所以就全部都是10。
这里就可以使用自执行匿名函数来解决。

```js
for(var i = 0; i < 10; i++) {
    (function(e) {
        setTimeout(function() {
            console.log(e);  
        }, 1000);
    })(i);
}
```
这样就将每个单独的i当做参数传入，就不会最后都引用同一个i了。


相关文章参考：
[Javascript的匿名函数与自执行](http://www.jcodecraeer.com/a/jquery_js_ajaxjishu/2012/0628/290.html)
[JavaScript 匿名函数有哪几种执行方式?](http://www.zhihu.com/question/20249179)
