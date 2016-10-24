title: 使用Vue快速开发单页应用
subtitle: 功能组件与路由组件通信
tags:
  - vue
  - spa
layout: post
categories: JS
date: 2016-08-25
---

> 本文所涉及代码全在[vue-cnode](https://github.com/wszgxa/vue-cnode)

![文件结构](https://segmentfault.com/image?src=http://7fvhwe.com1.z0.glb.clouddn.com/fileStruct.png&objectId=1190000006711743&token=2a95eb1caab2bdfd1b495e76acce1574)

上图是我们demo项目的整体结构，我们可以看见在第三级有三个东西router,全局组件，功能组件。其中全局组件和功能组件是脱离于router的，因为这两部分是所有逻辑组件共有的。我们将他提出来，放在App.vue里面而不是放在router下面的路由组件中，是为了只引用一次然后全局调用。

但是，具体的路由逻辑组件需要展现的全局组件内容是不一样的，需要功能组件交互的内容也不一样，所以需要在不同的路由逻辑组件中对全局组件和功能组件进行控制。

本文就是介绍如何通过vuex对全局组件和功能组件进行控制的。

<!-- more -->

## 使用vuex传递逻辑数据

说是控制，其实就是组件间交互，vue中组件交互有很多方式，我们这里用vuex。

我们将单独建立一个module(doc_state)，用来做功能组件和全局组件的功能交互，在module中我们将初始化功能组件或则全局组件需要的数据和函数方法等。如下图所示：

```
// doc state 需要一些变量来记录文档状态
import {
  SET_TIP
} from '../mutation_types'
const state = {
  tip: {
    text: '',
    time: 2000,
    callback: null
  }
}
const mutations = {
  [SET_TIP] (state, tip) {
    state.tip = tip
  }
}
export default {
  state,
  mutations
}
```
这里我们以tip（提示）组件为例子讲解，所有doc modules在`/src/vuex/modules/doc_state`。

然后我们定义doc_actions定义我们的action:

```
import * as types from '../mutation_types'

export const setTip = ({dispatch}, tip) => {
  if (!tip.time) tip.time = 2000 // 默认tip展现时间
  dispatch(types.SET_TIP, tip)
}
```
actions，会在路由逻辑组件中引用，调用action就能控制我们的tip组件。

再把 doc_state挂载到store.js就可以了～

```
import docState from './modules/doc_state'
export default new Vuex.Store({
  modules: {
    docState,
    content,
    userInfo
  },
  strict: debug,
  middlewares: debug ? [] : []
})
```

## tip组件

在我们的common文件夹里面新建一个tip.vue。他就是我们的提示信息组件。（路径`/src/components/common/tip.vue`）

首先我们需要获取tip在vuex里面的数据：

```
vuex: {
  getters: {
    tip: ({ docState }) => docState.tip
  }
},
```
然后就是具体的逻辑了：

```
data () {
  return {
    tipVisible: false
  }
},
watch: {
  tip: 'tipShow'
},
methods: {
  tipShow () {
    let self = this
    self.tipVisible = true
    setTimeout(function () {
      self.tipVisible = false
      if (self.tip.callback) {
        self.tip.callback()
      }
    }, self.tip.time)
  }
}
```
上面的tipVisible是我们用来控制控制展示的变量，在template里面：
``` html
<template>
  <div v-if="tipVisible" class="tip">
    <div class="tip-wrap">
      <p>{{ tip.text }}</p>
    </div>
  </div>
</template>
```

tip组件一共做了三件事：
1. 监听vuex获取的tip变量，在tip变量改变时执行tipShow方法。
2. 展示信息
3. 展示时间结束后，执行回调函数（如果有）

上面就完成了tip组件的整套逻辑，最后我们还需要把tip组件挂在App.vue。

template:
``` html
<template>
  <div id="app">
    <cn-header></cn-header>
    <sidebar></sidebar>
    <router-view></router-view>
    <tip></tip>
    <loading></loading>
  </div>
</template>
```
js:
``` js
import tip from './components/common/tip'
export default {
  components: {
    tip,
  },
}
```

这样我们就能在所有路由逻辑组件调用了。

## 调用方法

拿[登录组件](http://hiluluke.cn/2016/08/20/vue-first/)作为例子。

首先我们需要引入action:
```
import { setMenu, setTip } from '../../vuex/actions/doc_actions'
export default {
  vuex: {
    actions: {
      setTip,
      setDetail
    }
  }
}
```
我们在登录出错的时候会给用户提示信息：

``` js
this.setBaseInfo(this.access, (res) => {
  if (res.success) {
    this.success()
  }
  this.setTip({
    text: res.msg
  })
})
```

只要调用`this.setTip`方法就可以了。是不是很简单？组件写好后，后面大家再使用就只需调用这一个方法。


*注意*
这里需要主要传入回调函数的this的指向。建议这样：

``` js
this.setTip({
  text: 'lala',
  callback: () => {
    this.xxx()
  }
})
```
箭头函数有个作用就是会绑定它声明地方的this。这样我们就可以在callback里面调用当前组件的方法了。不用箭头函数，bind一下也是可以的～

## 小结

你可以发现我们的组件交互完全是通过数据去控制的。在搭建功能组件和vuex的module时会复杂一点，但是逻辑更清晰，我们在debug时候也更方便。在子组件调用时也非常的简单，就相当提供了一个接口。