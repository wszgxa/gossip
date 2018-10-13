title: JS函数式编程 - 函数组合与柯里化
tags:
- FP
- JS
layout: post
categories: FP
date: 2018-10-11
---

我们都知道单一职责原则，其实面向对象的SOLID中的S(SRP, Single responsibility principle)。在函数式当中每一个函数就是一个单元，同样应该只做一件事。但是现实世界总是复杂的，当把现实世界映射到编程时，单一的函数就没有太大的意义。这个时候就需要函数组合和柯里化了。

## 链式调用

如果用过jQuery的都晓得啥是链式调用，比如`$('.post').eq(1).attr('data-test', 'test')`.javascript原生的一些字符串和数组的方法也能写出链式调用的风格：


``` js
'Hello, world!'.split('').reverse().join('') // "!dlrow ,olleH"
```


首先链式调用是基于对象的，上面的一个一个方法`split`, `reverse`, `join`如果脱离的前面的对象"Hello, world!"是玩不起来的。

<!-- more -->

而在函数式编程中方法是独立于数据的，我们可以把上面以函数式的方式在写一遍：


``` js

const split = (tag, xs) => xs.split(tag)
const reverse = xs => xs.reverse()
const join = (tag, xs) => xs.join(tag)

join('', reverse(split('', 'Hello, world!'))) // "!dlrow ,olleH"

```

你肯定会说，你是在逗我。这比链式调用好在哪儿了？这里还是依赖于数据的啊，没有传递`'Hello, world!'，你这一串一串的函数组合也转不起来啊。这里唯一的好处也就是那几个单独的方法可以复用了。莫慌，后面还有那么多内容我怎么也会给你优化（忽悠）好的。再进行改造前，我们先介绍两个概念，部分应用和柯里化。

## 部分应用

部分应用是一种处理函数参数的流程，它会接收部分参数，然后返回一个函数接收更少的参数。这个就是部分应用。我们用`bind`来实现一把:

``` js
const addThreeArg = (x, y, z) => x + y + z;

const addTwoArg = addThreeNumber.bind(null, 1)
const addOneArg = addThreeNumber.bind(null, 1, 2)

addTwoArg(2, 3) // 6
addOneArg(7) // 10
```
上面利用`bind`生成了另外两个函数，分别接受剩下的参数，这就是部分应用。当然你也可以通过其他方式实现。

### 部分应用存在的问题

部分应用主要的问题在于，它返回的函数类型无法直接推断。正如前面所说，部分应用返回一个函数接收更少的参数，而没有规定返回的参数具体是多少个。这也就是一些隐式的东西，你需要去查看代码。才知道返回的函数接收多少个参数。


## 柯里化

柯里化定义：你可以调一个函数，但是只会传递一个参数给它。这个函数会返回一个函数去接收下**一个**参数。

``` js
const add = x => y => x + y
const plusOne = add(1)
plusOne(10) // 11
```

柯里化的函数返回一个只接收一个参数的函数，返回的函数类型可以预测。

当然在实际开发中，有很多的函数都不是柯里化的，我们可以使用一些工具函数来转化：
``` js
const curry = (fn) => { // fn可以是任何参数的函数
  const arity = fn.length;

  return function $curry(...args) {
    if (args.length < arity) {
      return $curry.bind(null, ...args);
    }

    return fn.call(null, ...args);
  };
};
```

也可以用开源库[Ramda](https://github.com/ramda/ramda)里提供的curry方法。

### 哦，柯里化。有什么用呢？

举个例子

``` js
const currySplit = curry((tag, xs) => xs.split(tag))
const split = (tag, xs) => xs.split(tag)

// 我现在需要一个函数去split ","

const splitComma = currySplit(',') //by curry

const splitComma = string => split(',', string)

```

可以看到柯里化的函数生成新函数时，和数据完全没有关系。对比两个生成新函数的过程，没有柯里化的相对而言就有一点啰嗦了。


## 函数组合

先给代码：
``` js
const compose = (...fns) => (...args) => fns.reduceRight((res, fn) => [fn.call(null, ...res)], args)[0];
```

其实compose做的事情一共两件：

1. 接收一组函数，返回一个函数，不立即执行函数
2. 组合函数，将传递给他的函数从左到右组合

可能有同学对上面的reduceRight不是很熟悉，我给个2元和3元的例子：
``` js
const compose = (f, g) => (...args) => f(g(...args))
const compose3 = (f, g, z) => (...args) => f(g(z(...args)))
```

函数调用是从左到右，数据流也是一样的从左到右。当然你可以定义从右到左的，不过从语义上来说就不那么表意了。

好，现在让我们来优化一下最开始的例子：
``` js
const split = curry((tag, xs) => xs.split(tag))
const reverse = xs => xs.reverse()
const join = curry((tag, xs) => xs.join(tag))

const reverseWords = compose(join(''), reverse, split(''))

reverseWords('Hello,world!');
```

是不是简洁易于理解多了。这里的`reverseWords`也是我们之前讲过的Pointfree的代码风格。不依赖数据和外部状态，就是组合在一起的一个函数。

Pointfree我在上一篇介绍过[JS函数式编程 - 概念](http://hiluluke.cn/2018/10/08/FP-with-JS-concept/)，也阐述了其优缺点，有兴趣的小伙伴可以看看。

### 函数组合的结合律

先回顾一下小学知识加法结合律：`a+(b+c)=(a+b)+c`。我就不解释了，你们应该能理解。

回过来看函数组合其实也存在结合律的：

``` js
compose(f, compose(g, h)) === compose(compose(f, g), h);
```


这个对于我们编程有一个好处，我们的函数组合可以随意组合并且缓存:

``` js
const split = curry((tag, xs) => xs.split(tag))
const reverse = xs => xs.reverse()
const join = curry((tag, xs) => xs.join(tag))

const getReverseArray = compose(reverse, split(''))

const reverseWords = compose(join(''), getReverseArray)

reverseWords('Hello,world!');
```

## 总结

对于链式调用和函数组合而言，其实两个各有各的好处，完全看个人喜好。我也总结两个的区别，和引申出来的pointfree代码风格。

OK，下一篇介绍一下范畴轮，和函子。