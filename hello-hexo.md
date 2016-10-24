title: Hello Hexo
tags: [hexo]
layout: post
categories: Tool
date: 2014-01-28
---

之前用的是[wordpress](http://cn.wordpress.org/),并搭建在sae里面。不过觉得一点也不好玩。具体和hexo那个好我就不比较了。其实都差不多，各有各自的优点。
在[简书](jianshu.io)看到的关于在github上搭建博客的教程，了解到hexo。再然后看到jacman这个theme就被吸引了。于是从零开始搭建自己通过hexo在github搭建blog。下面把我搭建学习相关步骤罗列下。
我是在网上看的各种教程搭建的，所以会比较多的外链啦~

## 熟悉github并配置

### 熟悉github

如果你知道如何使用github就绕过这一节吧。
开始我接触[github](https://github.com/)的时候全英文，也被唬住了。其实挺简单的。你就按照注册（注册我就不介绍了）后[set-up git帮助文档](https://help.github.com/articles/set-up-git)
<!--more-->
再然后看看这本书[pro git中文版](http://pan.baidu.com/s/1pJJjGIf)（密码：b9y5）。大概前3章基本上就能用了。

### 配置github，创建代码仓库

主要就是配置ssh，我看的这里的[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://jianshu.io/p/05289a4bc8b2)

## 安装hexo

### 安装nodejs

首先得安装nodejs，它是基于nodejs的。
不同系统不一样。
linux 下输入下面代码试试（不同发行版有区别）

``` bash
sudo apt-get install nodejs
```

windows直接去官网[nodejs](http://www.nodejs.org/)下载安装包安装。

### 安装hexo

具体安装方法就看[hexo系列教程：（二）搭建hexo博客](http://zipperary.com/2013/05/28/hexo-guide-2/)和[官网](hexo.io)的介绍。基本就没问题。

### 然后就是hexo的操作

看这篇博客吧[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://jianshu.io/p/05289a4bc8b2)

## jacman主题

这里面介绍了jacman里面的config文件各个参数的作用。
[how to use hexo](https://github.com/wszgxa/jacman)

## 域名

首先你得买个域名，我在[万网](http://www.net.cn/)买的(我比较了下价格，这里算便宜的。)。解析还是比较简单的。
相应域名提供商应该会带有解析的，万网买就自带的有，其他的不晓得。解析还得看下面这个官方的文档。
[官方的文档](https://help.github.com/articles/adding-a-cname-file-to-your-repository)和这篇博客[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://jianshu.io/p/05289a4bc8b2)

## about页面

如果你了解了hexo的使用，自己就琢磨出来了。
首先在主目录找到_config.yml,打开找到url

```yml
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://hiluluke.cn
root: /
permalink: :year/:month/:day/:title/
tag_dir: tags
about_dir: about
archive_dir: archives
category_dir: categories
code_dir: downloads/code
```
你的应该没有about_dir这个，加上。
然后在source里面建个文件夹取名about。里面建个index.md然后按照格式输入想要加的东西吧。做个参考下面是我index.md的内容。
```
title: About Me
duoshuo:
  enable: false
---

正文。。。。。。
```
