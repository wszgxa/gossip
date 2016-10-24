title: toString方法的仔细了解
tags:
- javascript
- 前端
layout: post
categories: JS
date: 2016-04-08
---

## 因子

在看Zepto源码的时候发现它做类型判断的时候用的下面的方法：
``` js
var a = {},
    toString = a.toString;

toString.call(function(){}); // [object Function]
toString.call({}); // [object Object]
toString.call(123); // [object Number]
toString.call("sasa"); // [object String]
```

上面这个判断类型的方法好像在js高程中好像看见过，不过忘了。然后让我对toString这个方法产生了好奇心，于是去多了解了下。
<!-- more -->



## 所有类型调用toString()的返回值

| 类型 | 返回值 |
|-----|------|
| 对象 | [object Obejct]  后面那个只为具体的对象 |
| 数组 | 逗号间隔的字符串 |
| 字符串 | 返回原字符串 |
| 函数 | 返回函数字符串 |
| 数字 | 数字字符串 |
| 正则 | 返回
| Math 对象 | [object Obejct] |
| Date | 完整的时间字符串 |
| boolen | 字符串的原值 |

## call改变引用对象后是咋样？

那对于其他的toString在通过call和apply改变this之后会是怎样呢？
我发现有些类型是这样的:

![](http://7fvhwe.com1.z0.glb.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202016-04-08%20%E4%B8%8B%E5%8D%885.48.19.png)

而有些类型像对象的一样返回[object xxxxx]

我试了试是不是引用类型和基本类型的区分。。。发现不是。不区分完了。还有其他事。

