---
title: vuex Store store 千万别写错了
date: 2019-11-26 18:11:13
tags:
  vuex
categories:
  bugfix

---

#### 问题：

刚才在使用vuex 的时候，碰到一个 this.$store 总是为 undefine 的问题

一开始的时候从控制台看到的是使用 action 的 this.$store.dispatch  的时候的报错



代码：

```js
    changeSourceSQL(sql) {
      this.$store.dispatch('updateSourceSQL', sql)
    },
```

报错信息

![](/Users/penghuilee/didi/Blog/source/images/vuex/1.png)



#### 分析过程：

**【分析】**

看到这种报错的时候， 肯定先进一步打印 dispatch 的上一级：

```js
    changeSourceSQL(sql) {
      console.log(sql, this.$store)
      
      this.$store.dispatch('updateSourceSQL', sql)
    },
```

结果是 undefined

![](/Users/penghuilee/didi/Blog/source/images/vuex/2.png)

**【分析】**

很明显这是因为$store 挂载到全局的时候失败了

此时，再去看 入口文件 index.js

代码：

```js
import "babel-polyfill"
import Vue from "vue"
import Vuex from "vuex"
import Store from "../store/index.js"
import VueRouter from "vue-router"
import Routers from "./router"
import App from "./app.vue"
import ViewUI from "view-design"
import "../my-theme/index.less"

Vue.use(Vuex)
Vue.use(VueRouter)
Vue.use(ViewUI)

console.log(Store)  // 调试的时候打印一下这个， 看是不是 Store 为 null

// 路由配置
const RouterConfig = {
  mode: "hash",
  routes: Routers
}
const router = new VueRouter(RouterConfig)

new Vue({
  el: "#app",
  router,
  Store,
  render: h => h(App)
})

```

 看上去没问题 ！！！！

并且打印的 Store 也有内容：

![](/Users/penghuilee/didi/Blog/source/images/vuex/3.png)



但是， store 就是没有挂载成功



看 API  ！！！

看 API  ！！！

看 API  ！！！



#### 找到原因：

原文：[vuex state](https://vuex.vuejs.org/zh/guide/state.html)

> 通过在根实例中注册 `store` 选项，该 store 实例会注入到根组件下的所有子组件中，且子组件能通过 `this.$store` 访问到



**文档里说是 store 选项， 对一定要看清楚，是 store 不是 Store**



#### 解决方案：

【解决】

把 index.js 里边的 Store 改成 store  完美运行

如果上边是写成

```js
import Store from "../store/index.js"
```

那么下边也可以写成

```js
new Vue({
  el: "#app",
  router,
  store: Store,
  render: h => h(App)
})
```



【最后，附上目录结构】

```
.
├── api
│   ├── base.js
│   └── index.js
├── components
│   ├── base-info.vue
│   ├── dataview.vue
│   ├── editer.vue
│   └── setkey.vue
├── my-theme
│   ├── base.less
│   └── index.less
├── pages
│   ├── app.vue
│   ├── index.html
│   ├── index.js
│   └── router.js
├── store
│   ├── actions.js
│   ├── getters.js
│   ├── index.js
│   ├── mutations.js
│   └── state.js
└── views
    ├── index.vue
    ├── relationship.vue
    ├── report.vue
    ├── transform.vue
    └── upload.vue
```



#### 总结：

【总结】

其实这是一个很小的问题， 但是也是体现了平时项目中不细心，自己去从零开始写 vuex的经验少，基本功不扎实等问题

多自己从零开始建项目，写项目可以很好的巩固自己的基本功