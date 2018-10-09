title: JS函数式编程 - 概念
tags:
- FP
- JS
layout: post
categories: FP
date: 2018-10-08
---

最近在看Typescript，顺便看了一些函数式编程，然后半个国庆假期就没有了。做个笔记，分几个部分写吧。

最开始接触函数式编程的时候，第一个接触的概念就是高阶函数，和柯里化。咋一看，这不就是长期用来讲作用域的demo吗？我在日常也有用啊，有啥吗？

其实呢，设计模式或则编程范式往往不在于技巧，而在于思想。函数式编程就是一种编程的范式，并不在于技巧多么叼，而在于它的思想。其次才是由设计思想才衍生出来的技巧，技巧往往而言是服务于思想的。所以我觉得最开始学习函数式编程最好先了解一些相关概念和思想会比较好。


## 函数是一等公民(first class)

如果理解直接看为一等公民的好处好处。

其实说函数式一等公民的意思就是说函数和其他“公民”具有相同的属性。就像任何一种数据类型，它能够被存储在数组中，能够作为函数的参数，能够赋值给变量:

``` js
const hello = (name) => (`Hello ${name}!`)
const sayHello = hello; // 作为变量 
const helloArray = [hello, sayHello]

```
上面的代码没有什么意义，只是表达函数在JavaScript中是一等公民，和一个值一样。


### 函数是一等公民的好处

拿一个callback的例子来讲，比如你用fetch发个请求时：
``` js
fetch('getPostLink')
  .then(res => renderPosts(res))
  .catch(err => handleError(error))
```
上面其实可以直接传递一个函数作为回调，加一层包裹其实没有必要：
``` js
fetch('getPostLink').then(renderPosts).catch(handleError)
```
多一层函数的包裹并没有任何意义，完全是多余的代码。再看一个例子：
``` js
const postController = {
  find(postId) { return Db.find(postId) },
  delete(postId) { return Db.delete(postId) },
  ...
}
```
上面的代码其实就是聚合一些功能作为一个对象，但是多加了一层的函数，也是没有必要的，在阅读的时候到会增加复杂度，其实`postController.find === Db.find`，所以完全没有再去包裹一层函数：
``` js
const postController = {
  find: Db.find,
  delete: Db.delete,
  ...
}
```
上面的代码是不是更表意，然而如果js的函数不能像值一样传递，上面的简写都是不可能的。上面的代码其实还有一个好处，你不用去纠结如何命名在两层函数之间的参数了。这种风格代码是符合Pointfree的，我们后面要介绍。另外，函数式编程是操作函数的，所以函数是一等公民也是函数式的基石，基本上如果js不支持这一项，函数式根本玩不转。

<!-- more -->

## 纯函数

让我举一个例子，大家在小学多学过一元一次方程吧：

```
f(x) = ax+b
```

这就是一个纯函数，一个输入然后返回一个输出。所有的东西都是围绕输入的，一个输入只可能返回一个输出，然后对任何其他没在作用域中的变量没有任何操作。

更书面的解释：一个纯函数一个输入永远都只有一个同样的输出，然后不会产生任何副作用。副作用是啥我们一会儿再说。

### 不纯的函数

通常不纯的函数分为两类，一种是会改变输入的：
``` js
const numbers = [1, 2, 3]
// 纯函数
numbers.slice(0, 3) // [1, 2, 3]
numbers.slice(1, 3) // [2, 3]
numbers.slice(0, 2) // [1, 2]
// 不纯的函数
numbers.splice(0, 3) // [1, 2, 3]
numbers.splice(0, 3) // []

```
上面中在numbers这个数组上面的两个方法，`slice`是纯函数。而splice则不是纯函数，它会改变输入的数值。做了额外的事。

另外一种是对函数以外的状态有依赖的：

``` js
let endpointForYoung = 18
// 不纯的函数
const checkYoungPeople = age => age <= endpointForYoung

// 纯函数
const checkYoungPeople = age => {
  const endpointForYoung = 18
  return age <= endpointForYoung
}

```

像上面的函数，第一个就是不纯的，他依赖的作用域之外的一个变量，一旦这个变量改变，这个函数返回的值就会跟着改变。

### 副作用

副作用就是在函数计算过程中，对函数外的状态进行更改或则与函数外状态进行交互的行为。首先副作用会导致函数不纯，是程序有不可控的依赖，不便于管理。但是，副作用是不可消除的，在正常的编程活动中是必然伴随着副作用的。所以在面对副作用时，问题不是如何消除副作用，而是如何管理副作用。这个，会在我们讲解范畴论相关概念的时候再深入。

正常编程活动中会引入的副作用有这些：
1. 文件读写操作
2. 增删改查数据库
3. http 请求
4. 打印log
5. 获得用户输入
6. 获取dom元素

当然不限于上面这几种，还有很多行为都带有副作用。


### 纯函数的好处

1. 能缓存

纯函数的每次输入和输出都是没有状态的，所以结果都一样，能够被缓存在任何地方而不会造成错误。

2. 可移植性，自文档

``` js
// 不纯函数
const signUp = (attrs) => {
  const user = saveUser(attrs);
  welcomeUser(user);
};

// 纯函数
const signUp = (Db, Email, attrs) => () => {
  const user = saveUser(Db, attrs);
  welcomeUser(Email, user);
};
```
第二个`signUp`依赖是从上传递的，所以能直观的看出`saveUser`需要`Db`，`welcomeUser`还需要`Email`。在不纯的函数中你很难在调用的时候知道他的依赖，你需要查看代码，才能搞清楚，“哦，原来还用了Db存了波数据啊。”

依赖作为参数传入，也很容易的在移植到其他场景使用，毕竟函数只是功能，针对不同场景操作不同的数据。


3. 易于测试

写单测的时候，最麻烦的就是如何mock数据。通常有两类数据最难mock，第一个是全局变量，比如`document`,另外一类是`import`进来的依赖，对于这两种，虽然在一些测试套件中有现成的工具库去mock。但是，都是很诡异的方式。

而如果是函数式的话，你测的就是一个输入一个输出，没有外部的影响，是非常容易测试的。

4. 并行代码

纯函数都是没有状态的，那即使跑在多台机器多个进程，每个单元相互之间是没有耦合关系的。


## Pointfree

大家可以看阮老师的这篇博客了解一下：http://www.ruanyifeng.com/blog/2017/03/pointfree.html

我只扯一下Pointfree风格代码的好处：
1. 正如上面所说的中间变量没有意义，不需要给变量命名
2. 代码更简洁，精炼，不用将过多的状态暴露给消费者

当然也有人认为其将太多的状态隐藏了，初读代码很难理解，只有看了具体函数实现功能才能知道真正的意图，对于代码的可读性而言，很糟糕。

这里有一处在hacker news上的讨论[Point-Free style: What is it good for?](https://news.ycombinator.com/item?id=1175946)。另外这还有一篇具体使用场景的文章[https://medium.freecodecamp.org/how-point-free-composition-will-make-you-a-better-functional-programmer-33dcb910303a](https://medium.freecodecamp.org/how-point-free-composition-will-make-you-a-better-functional-programmer-33dcb910303a)。有兴趣的小伙伴可以自己看看。


OK，下一篇介绍一下函数组合和柯里化。