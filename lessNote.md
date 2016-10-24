title: less note
tags:
- css
- less
layout: post
categories: Tool
date: 2015-10-20
---

less比较好入门，看了2-3个小时的文档，觉得已经7788了。东西并不是很多。这个笔记比较干，比较精简，建议还是看文档好些，反正不难。

### less基础

* less运行
其可以在html中嵌入一个less.js文件或则使用node工具lessc，也可以用gulp，grunt在部署之前编译。

**lessc**：
``` shell
sudo npm install -g lessc
```
然后就可以使用命令行编译less了。

```
lessc test.less test.css
```

上面代码就会将test.less编译输出在当前目录下test.css。

<!-- more -->
**gulp**

gulp下面有插件`gulp-less`可以在部署的时候编译less。

```js
var less = require('gulp-less');

gulp.task('less', function () {
  return gulp.src('./less/**/*.less')
    .pipe(less())
    .pipe(gulp.dest('./public/css'));
});
```

上面的代码就将./less下面所有文件夹里面以less结尾的文件全部编译输出到public/css下面。


* less变量
``` less
@base-color: red;
h1 {
    color: @base-color;
}
p{
    color: @base-color;
}
button {
    color: @base-color;
}
```
将被编译成
``` css
h1 {
    color: red;
}
p{
    color: red;
}
button {
    color: red;
}
```
* 混合变量

```less
.rounded-corners(arg) {
    border-radius: @arg;
}

p {
    .rounded-corners(10px);
}
```
将会被编译成：
```css
p {
    border-radius: 10px;
}
```
就是一种混合使用的方式，可以将其他类的样式在另外一个类里面添加。


* 通过嵌套让css变得更直接

```less
section {
    p {
        font-size: 10px;
    }
    h1 {
        font-size: 20px;
    }
}
```
将会编译成：

```css
section p {
    font-size: 10px;
}
section h1 {
    font-size: 20px;
}
```
* 操作变量

可以给变量进行数值运算。

```less
@basic-width: 800px;
.container {
    width: @basic-width;
    section {
        width: @basic-width * 2/3;
        background-color:red;
        color:white;
        float:left;
    }
    aside {
        width: @basic-width * 1/3;
        background-color: black;
        color: white;
        float: right;
    }
}
```

* 然后就是些内建的函数，主要是操作颜色的

* 命名空间

```less
.mixin(){
    color: red;
}
.mixin(){
    color: blue;
}
e1 {
    mixin
}
```

将被编译成：
```css
e1 {
color:red;
color:blue;
}
```

但是如果指定命名空间
```less
#namespace {
.mixin(){
    color: red;
}
}
.mixin(){
    color: blue;
}
e1 {
    #namespace > mixin;
}
```

就会指定空间下面进行编译
```css
e1 {
color: red;
}
```


笔记完。
