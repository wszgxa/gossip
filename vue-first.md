title: 使用Vue快速开发单页应用
subtitle: 登录页面
tags:
  - vue
  - spa
layout: post
categories: JS
date: 2016-08-20
---

> 本文所涉及代码全在[vue-cnode](https://github.com/wszgxa/vue-cnode)

前面两篇都是介绍组织结构和vue-router，从本文开始我们将讲一些如何优雅的用vue编写单页应用。首先我们从登录页面以及开始，因为后续很多其它的页面都需要登录信息。

## 路由，文件组织

首先我们第一步肯定是指定一条路由啦，在`src/config_router.js`里面增加一条路由
``` js
export function configRouter (router) {
  router.map({
    '/login': {
      name: 'login',
      title: '登录',
      // es6箭头函数
      component: (resolve) => require(['./components/login/login.vue'], resolve)
    }
  })

}
```
<!-- more -->
上面compenents部分，使用了webpack代码分割功能，这样他会安需加在`./components/login/login.vue`组件。也可以这样写`component: require('./components/login/login.vue')`，这样就不会按需载入了。编译的第一个文件就有这个。

然后我们需要在`src/conponengts/`下面创建一个login的文件夹一个login.vue的文件。最开始login.vue里面应该是这样的：

``` js
<style lang="styl" src='../../assets/styl/login.styl' scoped></style>

<template>
  <section class="content">
  // something
  </section>
</template>
<script>
  export default {

  }
</script>
```

上面是vue-loader的结构，你可以看到，样式、模版、js三者很清晰，不像react中的写法全是js，jsx感觉还有点混乱（个人观点）。

上面的style中`lang`是指定解析语言，我们这里用的是[stylus](http://stylus-lang.com/)，然后`src`指定文件路径，最后的一个scoped是指定样式只在当前组件生效。template和script里面的内容就部多介绍了，现在开始编写业务逻辑。

## vuex

这里我们不介绍[vuex](https://github.com/vuejs/vuex)是个啥，文档在[这里](http://vuex.vuejs.org/)。

我们需要保存登录的信息，因为在后续的所有接口中去获取数据我们都需要服务器返回的用户信息。这里我们将获取用户信息、保存用户信息逻辑全部拆出来交给vuex。

### modules

先安装vuex：`npm install vuex --save`，然后我们建一个`store`。在src文件夹下面创建`/vuex/modules/user_info.js：
``` js
// content
import {
  SET_BASEINFO,
  SET_DETAIL
} from '../mutation_types'
const state = {
  // 保存第一页数据
  id: '',
  accessToken: '',
  loginName: '',
  avatarUrl: '',
  score: '',
  recentTopics: '',
  recentReplies: '',
  github: '',
  createAt: ''
}

const mutations = {
  // 设置 token 登录名 头像
  [SET_BASEINFO] (state, data) {
    try {
      state.id = data.id
      state.accessToken = data.accesstoken
      state.loginName = data.loginname
      state.avatarUrl = data.avatar_url
    } catch (err) {
      console.log(err)
    }
  },
  [SET_DETAIL] (state, data) {
    try {
      state.score = data.score
      state.recentTopics = data.recent_topics
      state.recentReplies = data.recent_replies
      state.github = data.githubUsername || ''
      state.createAt = data.create_at
    } catch (err) {
      console.log(err)
    }
  }
}

export default {
  state,
  mutations
}

```

上面有个`mutation_types`是用来让mutation唯一的，在mutation_types里面是这样的：
``` js
// user info
export const SET_BASEINFO = 'SET_BASEINFO'
export const SET_DETAIL = 'SET_DETAIL'
```
用`const`来保证唯一性。

然后我们需要将modules加到store里面，新建一个`/src/vuex/store.js`文件：
```js
import Vue from 'vue'
import Vuex from 'vuex'
import userInfo from './modules/user_info'
Vue.use(Vuex)

const debug = process.env.NODE_ENV !== 'production'
export default new Vuex.Store({
  modules: {
    userInfo
  },
  strict: debug,
  middlewares: debug ? [] : []
})
```
再把其在App.vue里面引用，App.vue加入：
```js
import store from './vuex/store'
export default {
  store
}
```
这样我们就可以用:
```
vuex: {
  getters: {
    accessToken: ({ userInfo }) => userInfo.accessToken
  }
}
```
来获取用户数据了。

### actions

我们将获取用户信息，保存用户信息的逻辑全放在vuex的actions里面，而不是在业务逻辑里面获取，只是在vuex里面保存一下。这样业务逻辑就更扁平了，数据的获取只需要调用一下actions里面的函数，用getter来动态获取。具体代码如下：
```js
// /src/vuex/actions/user_actions.js
import * as types from '../mutation_types'
import { setMsg } from '../../tool'
import Vue from 'vue'
let localStorage = window.localStorage
let storeBaseInfo = (data) => {
  localStorage.setItem('cnodeBaseInfo', JSON.stringify(data))
}
let storeDetailInfo = (data) => {
  localStorage.setItem('cnodeDetailInfo', JSON.stringify(data))
}
// 获取存储在localStorage中的数据
export const getStore = ({ dispatch, state }) => {
  if (localStorage.getItem('cnodeBaseInfo')) {
    let data = JSON.parse(localStorage.getItem('cnodeBaseInfo'))
    dispatch(types.SET_BASEINFO, data)
  }
  if (localStorage.getItem('cnodeDetailInfo')) {
    let data = JSON.parse(localStorage.getItem('cnodeDetailInfo'))
    dispatch(types.SET_DETAIL, data)
  }
}
// 设置基础数据
export const setBaseInfo = ({ dispatch, state }, token, callback) => {
  Vue.http({
    url: '/api/v1/accesstoken',
    method: 'POST',
    body: JSON.stringify({
      accesstoken: token
    }),
    headers: {
      contentType: 'application/x-www-form-urlencoded'
    }
  }).then(function (res) {
    let data = JSON.parse(res.data)
    if (data.success) {
      data.accesstoken = token
      delete data.success
      storeBaseInfo(data)
      dispatch(types.SET_BASEINFO, data)
      callback(setMsg(true, '登录成功'))
    } else {
      console.log(data.error_msg)
      callback(setMsg(false, data.error_msg))
    }
  }).catch(err => {
    console.log(err)
    let errBody = JSON.parse(err.body)
    callback(setMsg(false, errBody.error_msg))
  })
}

```
上面有个setMsg的函数，只是用来将格式化返回给组件的内容的，把action写的就像一个接口一样。

上面用了[vue-resource](https://github.com/vuejs/vue-resource)。然后我们做的事主要就件，调用接口，拿数据，错误处理，保存数据到localStorage，返回数据。

接下来，我们看看组件里面的获取数据：
``` js
import dialog from '../common/dialog'
  import { setBaseInfo, setDetail } from '../../vuex/actions/user_actions'
  import { setMenu, setTip } from '../../vuex/actions/doc_actions'
  export default {
    vuex: {
      actions: {
        setBaseInfo,
        setMenu,
        setTip,
        setDetail
      }
    },
    data () {
      return {
        config: {
          visible: false,
          text: '在 Cnode社区网站端登录你的账户，然后在右上角找到【设置】按钮，点击进入后将页面滑动到最底部来查看你的Access Token。',
          sureText: '确定'
        },
        access: ''
      }
    },
    methods: {
      dialog () {
        this.config.visible = true
      },
      success () {
        window.history.go(-1)
        this.setMenu(true)
        this.setDetail()
      },
      login () {
        if (this.access.length < 6) {
          this.setTip({
            text: '请输入正确的Access Token'
          })
          return
        }
        this.setBaseInfo(this.access, (res) => {
          if (res.success) {
            this.success()
          }
          this.setTip({
            text: res.msg
          })
        })
      }
    },
    components: {
      dialog
    }
  }
```
首先引入actions,然后调用，因为返回数据格式事固定的，直接做判断，错误就用dialog弹错错误。是不是很清爽。

## 小结

我感觉要仔仔细细把所有全部讲完，篇幅太长了。。。大家还是看看我这做个指引，具体还是看[代码](https://github.com/vuejs/vue-router)