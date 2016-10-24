title: gulp note
tags:
- javascript
- 构建工具
layout: post
categories: Tool
date: 2015-10-14
---

前几天公司一个同事说了gulp用着太爽了，比grunt语法好很多。然后就猴急的尝试了下，并在公司之前的一个项目上做了实验。觉得有下面几点优点：

* 更像是就是js语法，写起来很顺
* 代码执行顺序更清晰
* 代码量更少，主要是因为grunt各种大小括号换行导致其看起来很臃肿。
* gulp.watch 太好用了。。

当然有几点grunt也是有优点，比如本省就支持在代码上面添加banner，但是gulp需要使用插件。

最后，推荐小伙伴也尝试着去使用gulp。下面是我自己的一些笔记。



### gulp常用插件
* less的编译[gulp-less](github.com/plus3network/gulp-less)
* 自动添加css前缀[gulp-autoprefixer](http://github.com/Metrime/gulp-autoprefixer)
* 压缩css[gulp-minify-css](http://github.com/jonathanepollack/gulp-minify-css)
* js代码校验[gulp-jshint](http://github.com/spenceralger/gulp-jshint)
* 给代码头部添加公司等之类的注释 [gulp-header](https://www.npmjs.com/package/gulp-header)
* 替换文本，用于修改版本号之类的 [gulp-replace](github.com/lazd/gulp-replace)
* 合并js文件[gulp-concat](http://github.com/wearefractal/gulp-concat)
* 压缩js代码[gulp-uglify](http://github.com/terinjokes/gulp-uglify)
* 压缩图片[gulp-imagemin](http://github.com/sindresorhus/gulp-imagemin)
* 自动刷新页面[gulp-livereload](http://github.com/vohof/gulp-livereload)
* 图片缓存，只有图片替换了才压缩[gulp-cache](http://github.com/jgable/gulp-cache)
* 更改提醒[gulp-notify](http://github.com/mikaelbr/gulp-notify)
* 清除文件[del](http://www.npmjs.org/package/del)


安装插件和grunt一样就用npm了，不啰嗦。

<!-- more -->
### 使用及api

当然和grunt一样需要创建任务文件，gulp的是gulpfile.js。

* 加载插件
与grunt不同插件需要用require引用加载。

```js
var gulp = require('gulp'),
    sass = require('gulp-ruby-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    minifycss = require('gulp-minify-css'),
    jshint = require('gulp-jshint'),
    uglify = require('gulp-uglify'),
    imagemin = require('gulp-imagemin'),
    rename = require('gulp-rename'),
    concat = require('gulp-concat'),
    notify = require('gulp-notify'),
    cache = require('gulp-cache'),
    livereload = require('gulp-livereload'),
    del = require('del');
```

#### gulp.src(globs[,options])

src指定需要处理的源文件的路径，借助unix的管道思想（pipe），前一级输出直接变成
后一级的输入，gulp.src返回当前文件流至可用插件；

* globs：需要处理的源文件匹配路径（必填）

可以是具体指定文件:"seca/a.js"
还有：``"*"`` 匹配所有文件
`"src/*.js"`（包含src下的所有js文件）
`"**"`匹配0个或者多个子文件夹
`"src/**/*.js"`(匹配多个子文件夹的js文件)
`"{}"`匹配多个属性`"src/{a,b}.js"`
`!`排除文件 （!src/a.js不包含src下的a.js文件）

* options 可选参数 opject 有3个属性buffer、read、base

options.buffer boolean类型，默认为true。当设置为false时，返回file.content的流
并且不缓冲文件，处理大文件时非常有用。

options.read：  类型：Boolean  默认：true 设置false，将不执行读取文件操作，返回null；
options.base：  类型：String  设置输出路径以某个路径的某个组成部分为基础向后拼接，具体看下面示例：

```js
gulp.src('client/js/**/*.js')
  .pipe(minify())
  .pipe(gulp.dest('build'));  // Writes 'build/somedir/somefile.js'

gulp.src('client/js/**/*.js', { base: 'client' })
  .pipe(minify())
  .pipe(gulp.dest('build'));  // Writes 'build/js/somedir/somefile.js'
```

#### gulp.dest(path[,options])

dest是在处理完后指定最后的输出路径。


path 必填，string 或则function 指定文件输出路径，或者定义函数返回文件输出路径亦可

options 可选，有两个属性，cwd,mode

options.cwd：  类型：String  默认：process.cwd()：前脚本的工作目录的路径 当文件输出路径为相对路径将会用到；
options.mode：  类型：String  默认：0777 指定被创建文件夹的权限；

#### gulp.task(name[,deps],fn)

定义gulp任务

name必填，string，指定任务名称

deps可选，stringArray，指定该任务依赖的任务（被依赖的任务需要返回当前任务的事件流=）
```js
gulp.task('testLess', function () {
    return gulp.src(['less/style.less'])
        .pipe(less())
        .pipe(gulp.dest('./css'));
});

gulp.task('minicss', ['testLess'], function () { //执行完testLess任务后再执行minicss任务
    gulp.src(['css/*.css'])
        .pipe(minifyCss())
        .pipe(gulp.dest('./dist/css'));
});
```
fn 必填：function该任务调用的插件操作。


#### gulp.watch(glob [, opts], tasks) or gulp.watch(glob [, opts, cb])

watch 用于监听文件变化，文件一修改就会执行指定的任务

glob：需要处理的源文件匹配符路径。

opts：可选：Object

tasks：必填，StringArray 需要执行的任务名称数组

cb(event) 可选：Function每个文件变化时执行的毁掉函数

```js
gulp.task('watch1', function () {
    gulp.watch('less/**/*.less', ['testLess']);
});

gulp.task('watch2', function () {
    gulp.watch('js/**/*.js', function (event) {
        console.log('File ' + event.path + ' was ' + event.type + ', running tasks...');
    });
});
```


主要就是task+dest+src+watch，然后再就是各种插件的使用，就ok了，上手很快，感觉3个小时左右就可以了。
