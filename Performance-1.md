title: 性能优化笔记
subtitle: 网络篇
tags:
  - 性能
  - 前端
layout: post
categories: JS
date: 2016-08-21
---

> 嗯，搞点大事情

记录一些大公司，高级前端工程师的前端优化经验总结，并尝试着做些实验，以后肯定用的上，当看到新的技能时，实时更新在这篇文章。

## DNS预解析

我们请求资源时，通常都是一个URL，拿到正确的ip地址必须经过一步DNS解析。DNS预解析的作用是在到某个页面是提前解析了DNS。用法都是这样：
``` html
<link rel="dns-prefetch" href="hiluluke.cn">
```
在很多大厂，比如jd.com、taobao.com等的head部分你就能看见很多上面这样的link标签。
然而其使用方法，并不是像上面这样简单，具体的看[这篇文章](https://segmentfault.com/a/1190000003944417)，本文不多做解释。

<!-- more -->
### 性能测试
实验url： 

* http://blog-c1000.codingapp.com/dns/index.html
* http://blog-c1000.codingapp.com/dns/entrance.html

上面两个URL第一个index里面有10个外链css资源，分别来自不同的网站。
第二个页面对entrance页面都对第一个页面css资源进行了DNS预解析。

实验流程:
* 清除dns缓存（mac下）：
```
sudo killall mDNSResponder
```
* 在chrome的chrome://net-internals/#dns中也把dns缓存清除掉：
* 在chrome里面dev-tool的network。然后打开第一个页面记录时间。
* 清除dns缓存，打开第二个页面。点击第二个页面链接条换到第一个页面，再次记录时间。

实验结果：

未进行dns预解析直接打开第一个页面时：
![未进行dns预解析](http://7fvhwe.com1.z0.glb.clouddn.com/dns1.png)

进行了dns预解析后跳转打开的页面:
![进行了dns预解析后](http://7fvhwe.com1.z0.glb.clouddn.com/dns2.png)

上图中第一个图片中深绿色就是dns预解析花费的时间，而在DNS预解析后第二个链接就没有了解析dns的过程。基本上节约的时间大概是300ms左右。

>未完待续

## 域名分区



<!--
## 减少重定向

## 使用CDN

## gzip压缩

## 无状态域名，不传输cookie

## 并行处理请求


## 客户端缓存资源
-->