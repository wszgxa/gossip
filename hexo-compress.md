title: 自动化部署hexo
tags:
  - hexo
  - 构建工具
layout: post
categories: Tool
date: 2016-8-19
---

作为一名小前端，对于页面的优化肯定是有追求的。本文将利用gulp来压缩hexo生成的文件，并利用npm命令自动部署我们的博客。

## 添加gulp
首先你需要全局安装gulp: `npm install -g gulp`。
<!-- more -->
然后在你的hexo根文件夹下增加一个gulpfile.js:

``` js
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');

// 压缩 public 目录 css
gulp.task('minify-css', function() {
  return gulp.src('./public/**/*.css')
    .pipe(minifycss())
    .pipe(gulp.dest('./public'));
});
// 压缩 public 目录 html
gulp.task('minify-html', function() {
  return gulp.src('./public/**/*.html')
    .pipe(htmlclean())
    .pipe(htmlmin({
      removeComments: true,
      minifyJS: true,
      minifyCSS: true,
      minifyURLs: true,
    }))
    .pipe(gulp.dest('./public'))
});
// 执行 gulp 命令时执行的任务
gulp.task('default', [
  'minify-html','minify-css'
]);
```
在上面如你所看到，有用4个node_modules，需要安装一下：
```
npm install --save-dev gulp
npm install --save-dev gulp-minify-css
npm install --save-dev gulp-htmlmin
npm install --save-dev gulp-htmlmin
```
另外我并没有压缩合并js，我觉得这是主题应该做的。因为hexo的html和css是hexo生成的，而js是最开始写好就直接移动到`public`的。我觉得压缩js应该主题做，比如这个[主题](https://github.com/wszgxa/hexo-theme-simple)。

## 添加自动部署命令

在package.json里面添加如下命令：

``` json
"scripts": {
  "build": "hexo clean && hexo g && gulp && hexo d"
},
```

然后运行`npm run build`，我们就会自动删除老文件，生成新文件，压缩html、css然后发布到github或其他静态服务器资源。
