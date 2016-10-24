title: javascript继承
tags:
- javascript
- 前端
layout: post
categories: JS
date: 2015-10-14
---

>本文主要是自己在阅读[javascript高级程序设计](http://www.ituring.com.cn/book/946)之后所做的笔记。


js的继承主要依靠原型链的方式实现，与一般的oo语言有些不一样。

### 原型链
利用原型让一个引用类型继承另一个引用类型的属性和方法。让原型对象等于另一个类型的实例。

```js
function SuperType(){
  this.property = true;
}
SuperType.prototype.getSuperValue = function(){
   return this.property;
};
function SubType(){
   this.subproperty = false;
}
//继承了 SuperType
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function (){
   return this.subproperty;
};  
var instance = new SubType();
alert(instance.getSuperValue());//true
```
<!--more-->
![原型链](http://7fvhwe.com1.z0.glb.clouddn.com/%E9%80%89%E5%8C%BA_008.png)


原型对象等于实例，即使得该对象原型的原型（从图中可以看出）指向实例的原型，从而继承了上一个对象的原型中的属性和方法。

#### 默认原型

所有引用类型都继承了Object

#### 确定原型和实例的继承关系

```js
alert(instance instanceof Object);//true
alert(instance instanceof SuperType);//true
alert(instance instanceof SubType);//true
alert(Object.prototype.isPrototypeOf(instance));//true
alert(SuperType.prototype.isPrototypeOf(instance));//true
alert(SubType.prototype.isPrototypeOf(instance));//true
```


#### 谨慎定义方法

* 子类型在需要重写超类型中某个方法，或则需要添加超类型中不存在的某个方法。这钟情况下，给原型添加方法的代码一定要放在替换原型的语句之后。

```js
function SuperType(){
	this.property = true;
}
SuperType.prototype.getSuperValue = function(){
	return this.property;
};
function SubType(){
	this.subproperty = false;
}
//继承了 SuperType
SubType.prototype = new SuperType();
//添加新方法
SubType.prototype.getSubValue = function (){
	return this.subproperty;
};
//重写超类型中的方法
SubType.prototype.getSuperValue = function (){
	return false;
};
var instance = new SubType();
alert(instance.getSuperValue());//false
```

就如同上面代码中定义顺序一样。

**另外需要注意，通过原型链实现继承时，不能通过对象字面量创建原型方法。因为这样会重写原型链。**

```js
function SuperType(){
	this.property = true;
}
SuperType.prototype.getSuperValue = function(){
	return this.property;
};
function SubType(){
	this.subproperty = false;
}
//继承了 SuperType
SubType.prototype = new SuperType();
//使用字面量添加新方法,会导致上一行代码无效
SubType.prototype = {
	getSubValue : function (){
	return this.subproperty;
},
someOtherMethod : function (){
	return false;
}
};
var instance = new SubType();
alert(instance.getSuperValue());
//error!
```
如上所示，就是用字面量的方式定义了原型，那样就重写了原型链。

#### 原型链的问题

引用类型值的问题，前面提过引用类型值会被所有实例**共享**。而在这里的通过原型来实现继承，原型实际上变成了另一个类型的实例。于是，原先的实例属性也就顺理成章的变成了现在的原型属性。

```js
function SuperType(){
	this.colors = ["red", "blue", "green"];
}
function SubType(){
}
//继承了 SuperType
SubType.prototype = new SuperType();
var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);//"red,blue,green,black"
var instance2 = new SubType();
alert(instance2.colors);//"red,blue,green,black"
```
在创建子类型的实例时，不能向超类型的构造函数中传递参数。

### 借用构造函数

* 思想：即在子类型构造函数内部调用超类型构造函数

```js
function SuperType(){
	this.colors = ["red", "blue", "green"];
}
function SubType(){
	//继承了 SuperType
	SuperType.call(this);
}
var instance1 = new SubType();
instance1.colors.push("black");
alert(instance1.colors);//"red,blue,green,black"
var instance2 = new SubType();
alert(instance2.colors);//"red,blue,green"
```

代码中加粗的那一行“借调”了超类型的构造函数。通过call()方法，我们在subtype实例环境下调用了supertype构造函数。


* 传递参数，借用构造函数有一个很大优势，即可以在子类型构造函数中向超类型中传递参数

```js
function SuperType(name){
	this.name = name;
}
function SubType(){
	//继承了 SuperType,同时还传递了参数
	SuperType.call(this, "Nicholas");
	//实例属性
	this.age = 29;
}
var instance = new SubType();
alert(instance.name);//"Nicholas";
alert(instance.age);//29
```

* 存在的问题
方法都在构造函数中定义，因此函数复用就无从谈起。

### 组合继承

* 将原型链和借用构造函数组合使用

```js
function SuperType(name){
	this.name = name;
	this.colors = ["red", "blue", "green"];
}
SuperType.prototype.sayName = function(){
	alert(this.name);
};
function SubType(name, age){
	//继承属性
	SuperType.call(this, name);
	this.age = age;
}
//继承方法
SubType.prototype = new SuperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function(){
   alert(this.age);
};
var instance1 = new SubType("Nicholas", 29);
instance1.colors.push("black");
alert(instance1.colors);//"red,blue,green,black"
instance1.sayName();//"Nicholas";
instance1.sayAge();//29
var instance2 = new SubType("Greg", 27);
alert(instance2.colors);//"red,blue,green"
instance2.sayName();//"Greg";
instance2.sayAge();//27
```

在这个例子中, SuperType 构造函数定义了两个属性: name 和 colors 。 SuperType 的原型定义了一个方法 sayName() 。 SubType 构造函数在调用 SuperType 构造函数时传入name 参数,紧接着又定义了它自己的属性 age 。然后,将 SuperType 的实例赋值给 SubType 的原型,然后又在该新原型上定义了方法 sayAge() 。这样一来,就可以让两个不同的SubType 实例既分别拥有自己属性——包括 colors 属性,又可以使用相同的方法了。

* 融合两者优点，最常用的继承模式


另外在[javascript高级程序设计](http://www.ituring.com.cn/book/946)上还有3种继承方式，原型式继承和寄生式继承和寄生组合继承。大家可以自己去看。


>本文为原创文章，转载请注明出处。
>本文地址：http://hiluluke.cn/2015/10/14/js-inherit/
