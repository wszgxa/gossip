title: 使用Vue快速开发单页应用
subtitle: vue-router
tags:
  - vue
  - spa
layout: post
categories: JS
date: 2016-08-05
---

> 本文所涉及代码全在[vue-cnode](https://github.com/wszgxa/vue-cnode)

vue-router主要作用是将路由控制，转移到前端。我们将会在vue-router里面保存一个路由表，在vue中具体通过调用提供的指令或则方法进行跳转。

其实吧，vue-router说白了就是一个插件，对外暴露的也就是指令和方法。如果只要求灵活用起来，我们需要学习的也就3个地方：
* 配置路由
* 指令
* 方法

<!--more-->
## 配置路由

路由配置其实是分两步的，第一步把vue-router的指令方法挂到Vue实例，第二步才是添加路由配置上。下面是基本的路由配置：
``` js
import Vue from 'vue'
import VueRouter from 'vue-router'
import App from 'App.vue'
Vue.use(VueRouter) // 第一步
const router = new VueRouter({
  history: true,
  saveScrollPosition: true
}) // 第二步
router.map({
    '/': {
      name: 'index',
      title: '全部',
      component: (resolve) => require(['./components/main/index.vue'], resolve)
    },
    '/good': {
      name: 'good',
      title: '精华',
      component: (resolve) => require(['./components/main/index.vue'], resolve)
    }
  })
router.start(App, '#app')
```
这里大家可以去看看源码，源码很简洁。主要就是声明了一个Router的对象，Router上面插件要的install方法，然后还有start方法。

源码片段
``` js
class Router {

  constructor ({
    hashbang = true,
    abstract = false,
    history = false,
    saveScrollPosition = false,
    transitionOnLoad = false,
    suppressTransitionError = false,
    root = null,
    linkActiveClass = 'v-link-active'
  } = {}) {
    ....
  }
Router.install = function (externalVue) {
  ....
  Vue = externalVue
  applyOverride(Vue)
  View(Vue)
  Link(Vue)
  util.Vue = Vue
}
```
源码中在Router一共有9个方法：

- map
- on
- redirect
- alias
- beforeEach
- afterEach
- go
- replace
- start
- stop

其中start是初始化组件的，

其中涉及路由前期配置的是：map, redirect，alias，beforeEach，afterEach，start。go和replace两个方法都是路由跳转的。

map，redirect，alias这3个方法是基础的配置方法，很简单，就请看[文档](http://router.vuejs.org/zh-cn/index.html)吧。

这里主要讲下beforeEach和afterEach，就和方法名一样他们分别是在路由跳转前后做的一些事情。

在vue-router有一个路由声明周期的概念，这里不展开来讲，你只需要知道我们berforeEach和afterEach后面接的函数是一个钩子函数，钩子函数接受的第一个参数是一个transition对象,transition对象有以下方法，主要是用来控制路由跳转的。

* transition.to 一个我们将要跳转去的路由的对象，这个对象可能有path,name等属性。
* transition.from 当前路由对象
* transition.next() 调用此函数处理切换过程的下一步
* transition.abort([reason]) 终止切换
* transition.redirect(path) 重定向到另一个路由

afterEach由于是路由切换后，只有上面的to,from两个属性。

为什么要单独讲beforEach和afterEach，因为我们可以在里面做很多全局的东西。比如登陆跳转、文章切换title、特定页面设置属性、调用vuex的方法等。这里我们讲一个刷新title的例子。后续还会有一个全局控制的文章，再补充些其他例子。

单页应用从始至终都只有会请求一个html，也就需要动态的改变，document title, 我们完全可以将修改title这个过程提到全局，在afterEach里面做，如下所示：

``` js
router.map({
    '/': {
      name: 'index',
      title: '全部',
      component: (resolve) => require(['./components/main/index.vue'], resolve)
    }
  }
  // document title change
  router.afterEach((transition) => {
    document.title = transition.to.title || 'CNode.js'
  })
```

在ios下面上述方法还不行，因为ios上有一个bug，不会再修改document.title时更新浏览器上面的title。因为这里并不是真正刷新页面，所以ios下不会调用浏览器更新title的事件吧。

可以用一个iframe的hack[方法](https://gist.github.com/wszgxa/48eefb02650ea011ab28a116643890a9)

## 指令

vue-router 提供的指令就一个：`v-link`。

他的作用就是跳转路由。具体用法如下：
``` html
<!-- 字面量路径 -->
<a v-link="'home'">Home</a>

<!-- 效果同上 -->
<a v-link="{ path: 'home' }">Home</a>

<!-- 具名路径 -->
<a v-link="{ name: 'user', params: { userId: 123 }}">User</a>
```
详细的用法建议看[文档](http://router.vuejs.org/zh-cn/link.html)

## 方法

vue在use vue-router之后就会添加一个$route的属性。里面有很多属性，主要的就是router下面的方法。
我们用的较多的是router.go,还有就是router.on。

这里还是建议看[文档](http://router.vuejs.org/zh-cn/api/go.html)，比我搬过来要详细的多。
