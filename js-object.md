title: javascript对象
tags:
- javascript
- 前端
layout: post
categories: JS
date: 2015-10-10
---


主要是因为再重新读了一遍[《javascript高级程序设计》](http://www.ituring.com.cn/book/946)（后文称其为js高）后对js的对象有了进一步认识，所以想写点东西总结下。

## js对象基础


### 理解对象

创建对象的方式有很多种，通常有以下三种：
* 通过创建一个Object实例然后再添加属性和方法
* 通过对象字面量的方式的方式：

```js
var person = {
	name: "Nicholas",
	age: 29,
	job: "Software Engineer",
	sayName: function(){
		alert(this.name);
 	}
};
```

* 通过函数的方式

```js
function Person(name, age, job){
	this.name = name;
	this.age = age;
	this.job = job;
	this.sayName = function(){
		alert(this.name);
	};
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
}
```

<!--more-->

主要都是构建特征值和方法来构建对象，表面上，上述方法构造的对象大同小异。然而其相差还是比较大的，另外构建对象实际生产中的方式其实也不止上面几种，js高书上就总结了7种。

### 属性类型

js对象中主要有两种属性：数据属性和访问器属性

#### 数据属性

其包含一个数据值的位置，可以读取和写入值。
其有4个可以描述其行为的特性：

* [[Configurable]]表示能否通过delete删除属性从而重新定义属性能否修改属性的特性，或则能否把属性修改为访问器属性。
* [[Enumerable]]表示能否通过for-in循环返回属性
* [[Writable]]表示能否修改属性的值
* [[Value]]包含这个属性的数据值

像上面那样在对象上定义属性，一般[[Configurable]]、[[Enumerable]]、[[[Writable]]特性都被设置成具体的值，而value被设置成具体的值，比如：

```js
var person = {
name: "Nicholas"
}
```
上面的name就被如上所述设置。

如果要修改属性的特性需要使用Object.definePrototype()方法。该方法接收3个参数，属性所在的对象、属性的名字和一个描述符对象。

```js
var person = {};
Object.defineProperty(person, "name", {
    writable: false,
     value: "Nicholas"
});
alert(person.name);//"Nicholas"
person.name = "Greg";
alert(person.name);//"Nicholas"
```

可以重复使用上面的方法修改特性，但是如果将configurable特性设置成false后就有限制了。


#### 访问器属性

不包含数值，包含一对getter和setter函数。读取数据，调用getter负责返回有效的值；写入调用setter函数传入新值，负责决定如何处理数据。

4个特性：
* [[Configurable]][[Enumerable]]和上面的一样
* [[Get]]在读取属性时调用的函数
* [[Set]]在写入属性时调用的函数

访问器属性不能直接定义，必须使用Object.definePrototype()
```js
var book = {
  _year: 2004,
  edition: 1
};
Object.defineProperty(book, "year", {
  get: function(){
     return this._year;
  },
  set: function(newValue){
     if (newValue > 2004) {
     this._year = newValue;
     this.edition += newValue - 2004;
  }
}
});
book.year = 2005;
alert(book.edition);//2
```

#### 定义多个属性

Object.defineProperties()可以定义多个属性

#### 读取属性特性

Object.getOwnPropertyDescriptor()可以获取给定属性的描述符。这个方法接收两个参数：属性所在对象和要读取其描述符的属性名称，返回一个对象：

```js
var book = {};
Object.defineProperties(book, {
  _year: {
    value: 2004
  },
edition: {
   value: 1
},
year: {
  get: function(){
  return this._year;
},
set: function(newValue){
   if (newValue > 2004) {
   this._year = newValue;
   this.edition += newValue - 2004;
  }
 }
}
});
var descriptor = Object.getOwnPropertyDescriptor(book, "_year");
alert(descriptor.value);//2004
alert(descriptor.configurable); alert(typeof descriptor.get);//"undefined"
var descriptor = Object.getOwnPropertyDescriptor(book, "year");
alert(descriptor.value);//undefined
alert(descriptor.enumerable);//false
alert(typeof descriptor.get);//"function"
```

## 多种对象创建方式

### 创建对象、工厂模式

这种模式抽象创建具体对象过程

```js
function createPerson(name, age, job){
	var o = new Object();
	o.name = name;
	o.age = age;
	o.job = job;
	o.sayName = function(){
		alert(this.name);
	};
	return o;
}
var person1 = createPerson("Nicholas", 29, "Software Engineer");
var person2 = createPerson("Greg", 27, "Doctor");
```

可以无数次调用这个函数，每次它都会返回一个包含3个属性和一个方法的对象。

**问题**

上述方法有一个问题没有解决，对象识别的问题（即你对象类型的定义）。

### 创建对象、构造函数模式

创建自定义构造函数，从而定义自定义对象属性和方法

```js
function Person(name, age, job){
   this.name = name;
   this.age = age;
   this.job = job;
   this.sayName = function(){
   alert(this.name);
  };
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

上面的方法就像Object和Array一样使用new新建对象，这种方式能够识别对象类型。

另外与工厂模式还有以下不同
* 没有显示的创建对象，即没有用一个createPerson的函数
* 直接将属性和方法赋给this对象
* 没有return

上面例子都保存着Person的一个不同实例。都有一个constructor属性，这个属性指向Person。

```js
alert(person1.constructor == Person);//true
alert(person2.constructor == Person);//true
```

构造函数可以当做函数使用，这时候会将属性添加到window下
```js
var person = new Person("Nicholas", 29, "Software Engineer");
person.sayName(); //"Nicholas"
// 作为普通函数调用
Person("Greg", 27, "Doctor"); // 添加到 window
window.sayName(); //"Greg"
// 在另一个对象的作用域中调用
var o = new Object();
Person.call(o, "Kristen", 25, "Nurse");
o.sayName(); //"Kristen"
```

**问题**

主要问题是每个方法都要在每个实例上重新创建一遍。
```js
function Person(name, age, job){
   this.name = name;
   this.age = age;
   this.job = job;
   this.sayName = new Function("alert(this.name)"); // 与声明函数在逻辑上是等价的
}
```

因为每个Person实例豆瓣韩一个不同的Function实例，所以这种方式创建的函数sayName有不同的作用域链和标识符解析。也就是不同实例的同名函数是不相等的。

```js
alert(person1.sayName == person2.sayName);//false
```

在每次创建对象时都创建一个相同的方法是没有必要的。

可以像下面这样解决这个问题：
```js
function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = sayName;
}
function sayName(){
  alert(this.name);
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
```

将sayName定义转移到构造函数外部，在内部将sayName设置成等于全局sayName函数，sayName是一个指向函数的指针，这也使得person1和person2共享同一个sayName函数。

### 创建对象，原型模式

原型能让对象实例共享它所包含的属性和方法。可以不在构造函数中定义对象实例信息，而是将这些信息直接添加到原型对象中：

```js
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
alert(this.name);
};
var person1 = new Person();
person1.sayName();//"Nicholas"
var person2 = new Person();
person2.sayName();//"Nicholas"
alert(person1.sayName == person2.sayName）;//true
```

#### 理解原型对象

* 只要创建新函数，就会根据一组特定规则创建一个该函数的prototype属性，这个属性指向原型对象。

* 所有原型对象都会自动获得一个constructor属性，，这个属性包含一个指向prototype属性所在函数的指针。

![js原型](http://7fvhwe.com1.z0.glb.clouddn.com/选区_006.png)

原型实例之间的关系就如上图所示，原型对象默认只会取得constructor属性，至于其他方法则都是从Object继承而来。当调用构造函数创建一个新实例后，该实例的内部将包含一个指针(内容属性)指向构造函数的原型对象。

* 我们无法访问[[Prototype]]属性，但是可以用isPrototypeOf()方法来确定对象之间是否存在这种关系。
```js
alert(Person.prototype.isPrototypeOf(person1));//true
alert(Person.prototype.isPrototypeOf(person2));//true
```

* 后期js又增加了一个方法Object.getPrototypeOf()，返回[[prototype]]值。

* 可以通过实例访问原型中的值，但是不能重写原型中的值。当在实例中创建一个和原型同名的值时，将在实例中创建该属性，然后调用时候会覆盖原型中的值。

```js
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
alert(this.name);
};
var person1 = new Person();
var person2 = new Person();
person1.name = "Greg";
alert(person1.name);//"Greg"——来自实例
alert(person2.name);//"Nicholas"——来自原型
```

* 当我们设置实例中的同名值为null时，并不会恢复指向原型的连接。我们需要使用delete餐能完全删除实例属性。

```js
function Person(){
}
Person.prototype.name = "Nicholas";
Person.prototype.age = 29;
Person.prototype.job = "Software Engineer";
Person.prototype.sayName = function(){
alert(this.name);
};
var person1 = new Person();
var person2 = new Person();
person1.name = "Greg";
alert(person1.name);//"Greg"——来自实例
alert(person2.name); //"Nicholas"——来自原型
delete person1.name;
alert(person1.name); //"Nicholas"——来自原型
```


* 确定属性是存在对象还是原型中：
```js
function hasPrototypeProperty(object, name){
   return !object.hasOwnProperty(name) && (name in object);
}
```
上面使用了in和hasOwnproperty，大家可以百度之，个人觉得不是常用。


* 更简单的原型语句

```js
function Person(){
}
Person.prototype = {
   name : "Nicholas",
   age : 29,
   job: "Software Engineer",
   sayName : function () {
   alert(this.name);
 }
};
```

上述方式和之前的方式产生相同的结果，不过constructor属性不再指向Person了。每创建一个函数就会创建prototype，然后自动获得constructor。但是上面这个重写了默认的prototype对象，因此constructor属性也就变成了新对象的constructor属性（指向Object构造函数）。

如果constructor重要，我们可以设置constructor：Person来指代回去。

* 原型的动态性

在原型中查找值，是一次搜索。所以如果我们先创建实例，再修改原型，也能在实例中反应出来。

```js
var friend = new Person();
Person.prototype.sayHi = function(){
alert("hi");
};
friend.sayHi();//"hi"(没有问题!)
```

如果重写整个原型对象就不一样了。

> 这里说说啥叫重写整个原型对象
> 只是设置方法Person.prototype.sayHi=function(){}，没有重写整个对象
> 但是这样Person.prototype={}就是重写整个对象
> 重写整个prototype对象将会修改其默认的构造方式

调用构造函数时会为实例添加一个指向最初原型的[[Prototype]]指针，而把原型修改为另外一个对象切断了构造函数与最初原型之间的联系。

```js
function Person(){ }
	var friend = new Person();
	Person.prototype = {
		constructor: Person,
		name : "Nicholas",
		age : 29,
		job : "Software Engineer",
		sayName : function () {
			alert(this.name);
		}
	};
friend.sayName();//error
```

![重写原型对象](http://7fvhwe.com1.z0.glb.clouddn.com/选区_007.png)

可以看出重写原型对象切断了现有原型与任何之前已经存在的对象实例之间的联系;它们引用的仍然是最初的原型。

**我觉得就是尽量不要重写原型对象，没有啥好处还破事特多**

* 原生对象的原型

所有原生的引用类型（理解成对象吧）都存在原型。比如Array.prototype中有sort方法。`alert(typeof Array.prototype.sort);//"function"`

这里可以利用原型给原生的引用类型添加方法。

* **原型对象的问题**

我们的属性值，将都是引用的，不同的实例共享相同的属性值。

```js
function Person(){
}
Person.prototype = {
   constructor: Person,
   name : "Nicholas",
   age : 29,
   job : "Software Engineer",
   friends : ["Shelby", "Court"],
   sayName : function () {
   alert(this.name);
  }
 };
var person1 = new Person();
var person2 = new Person();
person1.friends.push("Van");
alert(person1.friends);//"Shelby,Court,Van"
alert(person2.friends);//"Shelby,Court,Van"
alert(person1.friends === person2.friends); //true
```

因为上面的friends是引用类型，当修改其值时，就会反应到所有实例中。

### 组合使用构造函数模型和原型模式（最常用）

```js
function Person(name, age, job){
  this.name = name;
  this.age = age;
  this.job = job;
  this.friends = ["Shelby", "Court"];
}
Person.prototype = {
   constructor : Person,
   sayName : function(){
   alert(this.name);
 }
}
var person1 = new Person("Nicholas", 29, "Software Engineer");
var person2 = new Person("Greg", 27, "Doctor");
person1.friends.push("Van");
alert(person1.friends);//"Shelby,Count,Van"
alert(person2.friends);//"Shelby,Count"
alert(person1.friends === person2.friends);//false
alert(person1.sayName === person2.sayName);//true

```

上面将构造函数和原型对象结合使用，是现在js中常用的方式，解决了3个问题：

* 对象类型的定义，对象识别问题
* 值不共享
* 方法共享

**认可度最高的方式**

### 动态原型模式

在构造函数之外定义一个原型，这种方式在其他OO语言中看起来比较奇怪，下面的方法主要就是解决这个问题，将原型也放在构造函数中。

```js
function Person(name, age, job){
  //属性
  this.name = name;
  this.age = age;
  this.job = job;//方法
  if (typeof this.sayName != "function"){
     Person.prototype.sayName = function(){
     alert(this.name);
  };
 }
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName();
```
注意上述代码if段，加if判断是为了让原型初始化只有一次，这样就不会在每次初始化实例的时候都创建一个sayname函数。

### 寄生构造函数模式

```js
function Person(name, age, job){
   var o = new Object();
   o.name = name;
   o.age = age;
   o.job = job;
   o.sayName = function(){
   alert(this.name);
 };
 return o;
}
var friend = new Person("Nicholas", 29, "Software Engineer");
friend.sayName(); //"Nicholas"
```

构造函数在不返回值的情况下，默认返回新对象实例。而通过构造函数末尾添加一个return 可以重写构造函数时返回的值。


### 稳妥构造函数模式

```js
function Person(name, age, job){
//创建要返回的对象
var o = new Object();
//可以在这里定义私有变量和函数
//添加方法
o.sayName = function(){
alert(name);
};
//返回对象
return o;
}
```

注意,在以这种模式创建的对象中,除了使用 sayName() 方法之外,没有其他办法访问 name 的值。
可以像下面使用稳妥的 Person 构造函数。
var friend = Person("Nicholas", 29, "Software Engineer");
friend.sayName(); //"Nicholas"


## 小结


上面的对象构造函数很多种，不过生产中应该主要是使用的*构造函数和原型结合*的方式。

其他几种有相应的优缺点，个人觉得了解就可以了。在看别人代码知道是怎么回事就行了。

另外构造函数和原型结合的方式一般和[自执行匿名函数](http://hiluluke.cn/2015/02/21/jsselfdo/)结合使用，以免污染全局环境。
通常的形式是这样的。

```js
(function($){
	function Person(name, age, job){
		this.name = name;
		this.age = age;
		this.job = job;
		this.friends = ["Shelby", "Court"];
	}
	Person.prototype = {
		constructor : Person,
		sayName : function(){
			alert(this.name);
 		}
	}
	window.Person = Person;
})(jQuery)

```

上面的jQuery是外部传递进去的参数，可以在内部使用。最后只将Person添加到window这样就不会污染全局作用域。


重新梳理一遍又有新的收获，下一篇是继承。主要的内容都是来自[《javascript高级程序设计》](http://www.ituring.com.cn/book/946)这本书，个人觉得其比《javascript权威指南》好，如果你买这种柜台书，建议买这本。

文章后续还会更新，有问题请在评论指正提出。

>本文为原创文章，转载请注明出处。
>本文地址：http://hiluluke.cn/2015/10/10/js-object/
