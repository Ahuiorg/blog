---
title: vue常见API原理分析
abbrlink: 3258801602
date: 2020-07-04 10:14:41
tags: [vue]
---

### 数据绑定

<!-- more -->

#### oberserver

`Object.defineProperty()`

#### dep

`dep.notify()`

#### watcher

`user-watcher`  `render-watcher`  `computed-watcher`

### nextTick

> `nextTick` 会在`DOM`更新完毕之后执行一个回调，确保我们操作的是更新之后的`DOM`

`vue`用异步队列的方式来控制`DOM`更新和`nextTick`回调先后执行
`microtask`因为其高优先级特性，能确保队列中的微任务在一次事件循环前被执行完毕
因为兼容性问题，`vue`不得不做了`microtask`向`macrotask`的降级方案 (`Promise`，`MutationObserver`，`setTimeout`)

1. MutationObserver 

用于监听`DOM`修改事件，能够监听到节点的属性，文本内容，子节点等的改动

2. Event Loop

MutationObserver 每次监听到变更的时候会往 microtask 添加一个事件

3. 降级方案

Promise => MutationObserver => setTimeout


[参考文档>>>](https://www.cnblogs.com/liuhao-web/p/8919623.html)

### computed

```
initData()
initCompunted()
defineComputed()
Object.defineProperty()
get: createCompuntedGetter()
watcher()
watcher.evaluate()
watcher.depend()
```

> 这里的变量`watcher`就是之前`computed`对应的`computed-watcher`实例，接下来会执行`Watcher`类专门为计算属性定义的两个方法，在执行`evaluate`方法进行求值的过程中又会触发`computed`内可以访问到的响应式数据的`get`，它们会将当前的`computed-watcher`作为依赖收集到自己的`dep`里，计算完毕之后将`dirty`置为`false`，表示已经计算过了。

> 然后执行`depend`让计算属性内的响应式数据订阅当前的`render-watcher`，所以`computed`内的响应式数据会收集`computed-watcher`和`render-watcher`两个`watcher`，当`computed`内的状态发生变更触发`se`t后，首先通知`computed`需要进行重新计算，然后通知到视图执行渲染，再渲染中会访问到`computed`计算后的值，最后渲染到页面。

为什么计算属性有缓存功能？

因为当计算属性经过计算后，内部的标志位会表明已经计算过了，再次访问时会直接读取计算后的值

为什么计算属性内的响应式数据发生变更后，计算属性会重新计算？

因为内部的响应式数据会收集`computed-watcher`，变更后通知计算属性要进行计算，也会通知页面重新渲，渲染染时会读取到重新计算后的值。

### watcher

```
watch监听属性收集依赖过程

root => _init()    根组件初始化

...

root => vm._update(vm._render())   根组件渲染，没状态不用收集依赖
  app => initData()                 初始化App组件的data
  app => oberserver(name)           将name转为响应式
  app => initWatch(watch)           初始化watch
  app => this.$watch('name')        主要就是这里： 触发name的get， 让dep收集user-watcher 
  app => Sub.$mont()                子组件挂载
  app => new Watcher(vm, geeter)    实例化 render-watcher
  app => vm._render()               触发name的get， 让dep收集render-watcher

  ...

  watch监听属性派发更新

  app => name = 'www'               响应式数据被贱赋值，触发set
  app => dep.nofity()               dep通知收集到的watcher
  app => user-watcher               user-watcher派发新值跟旧值给回调函数
  app => render-watcher             redner-wachter改变视图

```

`watch`和`this.$watch`的实现是一致的，以及简单解释它的原理就是为需要观察的数据创建并收集·，当数据改变时通知到·将新值和旧值传递给用户自己定义的回调函数。

定义watch时会被使用到的三个参数：`sync`、`immediate`、`deep`

简单说明它们的实现原理就是：`sync`是不将watcher加入到nextTick队列而同步的更新、`immediate`是立即以得到的值执行一次回调函数、`deep`是递归的对它的子值进行依赖收集。


### 虚拟DOM生成真实DOM的过程

1. 元素节点生成`Dom`

里向外的挨个创建出真实的`Dom`，然后插入到它的父节点内，最后将创建好的`Dom`插入到`body`内，完成创建的过程

2. 组件VNode生成`Dom`

> 无论是嵌套多么深的组件，遇到组件的后就执行`init`，在`init`的`__patch__`过程中又遇到嵌套组件，那就再执行嵌套组件的`init`，嵌套组件完成`__patch__`后将真实的`Dom`插入到它的父节点内，接着执行完外层组件的`__patch__`又插入到它的父节点内，最后插入到`body`内，完成嵌套组件的创建过程，总之还是一个由里及外的过程


### extend和$mount

这两个都是`vue`提供的API，不过在平时的业务开发中使用并不多。在`vue`的内部也有使用过这一对API。遇到嵌套组件时，首先将子组件转为组件形式的VNode时，会将引入的组件对象使用extend转为子组件的构造函数，作为VNode的一个属性Ctor；然后在将VNode转为真实的`Dom`的时候实例化这个构造函数；最后实例化完成后手动调用$mount进行挂载，将真实`Dom`插入到父节点内完成渲染。

#### extend 

> 接受的是一个组件对象，再执行extend时将继承基类构造器上的一些属性、原型方法、静态方法等，最后返回Sub这么一个构造好的子组件构造函数。拥有和`vue`基类一样的能力，并在实例化时会执行继承来的_init方法完成子组件的初始化。

#### 实例化Sub

> 执行_init组件初始化的一系列操作，初始化事件、生命周期、状态等等。将data或props内定义的变量挂载到当前this实例下，最后返回一个实例化后的对象。

#### $mount

> 在得到初始化后的对象后，开始组件的挂载。首先将当前render函数转为VNode，然后将VNode转为真实Dom插入到页面完成渲染。再完成挂载之后，会在当前组件实例this下挂载$el属性，它就是完成挂载后对应的真实Dom，我们就需要使用这个属性。

### 常见问题

1. 请问`runtime-compiler`和`runtime-only`这两个版本的区别？

> `runtime-compiler` 经历的是一个 `template -> ast -> render -> vdom -> UI` 的过程，
> `runtime-only`直接使用了`render`函数，所以经历的是一个 `render -> vdom -> UI` 的过程。 `runtime-only` 会省去一个 `template -> ast -> render` 的过程，也不再需要相关的`loader`插件，从而这种方法搭建的项目性能更高，代码也更少，项目大小也更小。
> 最明显的就是大小的区别，带编译器会比不带的版本大6kb。

2. 请问可以在`beforeCreate`钩子内通过`this`访问到`data`中定义的变量么，为什么以及请问这个钩子可以做什么？

> 是不可以访问的，因为在`vue`初始化阶段，这个时候`data`中的变量还没有被挂载到`this`上，这个时候访问值会是`undefined`。
> `beforeCreate`这个钩子在平时业务开发中用的比较少，而像插件内部的`instanll`方法通过`Vue.use`方法安装时一般会选在`beforeCreate`这个钩子内执行，`vue-router`和`vuex`就是这么干的。


3. 请问`methods`内的方法可以使用箭头函数么，会造成什么样的结果？

> 是不可以使用箭头函数的，因为箭头函数的`this`是定义时就绑定的。
> 在vue的内部，`methods`内每个方法的上下文是当前的vm组件实例，`methods[key].bind(vm)`，而如果使用使用箭头函数，函数的上下文就变成了父级的上下文，也就是`undefined`了，结果就是通过`undefined`访问任何变量都会报错。


4. 请问vue@2为什么要引入虚拟Dom，谈谈对虚拟Dom的理解？

> 随着现代应用对页面的功能要求越复杂，管理的状态越多，如果还是使用之前的JavaScript线程去频繁操作GUI线程的硕大Dom，对性能会有很大的损耗，而且也会造成状态难以管理，逻辑混乱等情况。引入虚拟Dom后，在框架的内部就将虚拟Dom树形结构与真实Dom做了映射，让我们不用在命令式的去操作Dom，可以将重心转为去维护这棵树形结构内的状态即可，状态的变化就会驱动Dom发生改变，具体的Dom操作vue帮我们完成，而且这些大部分可以在JavaScript线程完成，性能更高
>虚拟Dom只是一种数据结构，可以让它不仅仅使用在浏览器环境，还可以用与SSR以及Weex等场景。

5. 父子两个组件同时定义了`beforeCreate`、`created`、`beforeMounte`、`mounted`四个钩子，它们的执行顺序是怎么样的？

> 首先会执行父组件的初始化过程，所以会依次执行`beforeCreate`、`created`、在执行挂载前又会执行`beforeMounte`钩子，不过在生成真实dom的`__patch__`过程中遇到嵌套子组件后又会转为去执行子组件的初始化钩子`beforeCreate`、`created`，子组件在挂载前会执行`beforeMounte`，再完成子组件的Dom创建后执`行mounted`。这个父组件的`__patch__`过程才算完成，最后执行父组件的`mounted`钩子，这就是它们的执行顺序。执行顺序如下：

```
parent beforeCreate
parent created
parent beforeMounte
    child beforeCreate
    child created
    child beforeMounte
    child mounted
parent mounted
```

6. 当前组件模板中用到的变量一定要定义在data里么？

> data中的变量都会被代理到当前this下，所以我们也可以在this下挂载属性，只要不重名即可。而且定义在data中的变量在vue的内部会将它包装成响应式的数据，让它拥有变更即可驱动视图变化的能力。但是如果这个数据不需要驱动视图，定义在created或mounted钩子内也是可以的，因为不会执行响应式的包装方法，对性能也是一种提升。

7. 请简单描述下vue响应式系统？

> 简单来说就是使用Object.defineProperty这个API为数据设置get和set。  当读取到某个属性时，触发get将读取它的组件对应的render watcher收集起来；当重置赋值时，触发set通知组件重新渲染页面。
> 如果数据的类型是数组的话，还做了单独的处理，对可以改变数组自身的方法进行重写，因为这些方法不是通过重新赋值改变的数组，不会触发set，所以要单独处理。
> 响应系统也有自身的不足，所以官方给出了$set和$delete来弥补。

8. 为什么v-for里建议为每一项绑定key，而且最好具有唯一性，而不建议使用index？

> diff比对内部做更新子节点时，会根据oldVnode内没有处理的节点得到一个key值和下标对应的对象集合，为的就是当处理vnode每一个节点时，能快速查找该节点是否是已有的节点，从而提高整个diff比对的性能。
> 如果是一个动态列表，key值最好能保持唯一性，但像轮播图那种不会变更的列表，使用index也是没问题的。

9. 说下自定义事件的机制。

> 子组件使用this.$emit触发事件时，会在当前实例的事件中心去查找对应的事件，然后执行它。不过这个事件回调是在父组件的作用域里定义的，所以$emit里的参数会传递给父组件的回调函数，从而完成父子组件通信。

10. 请说明下组件库中命令式弹窗组件的原理？

> 使用`extend`将组件转为构造函数，在实例化这个这个构造函数后，就会得到`$el`属性，也就是组件的真实`Dom`，这个时候我们就可以操作得到的真实的`Dom`去任意挂载，使用命令式也可以调用。

11. 请说明下`transition`组件的实现原理？

> `transition`组件是一个抽象组件，不会渲染出任何的`Dom`，它主要是帮助我们更加方便的写出动画。
> 以插槽的形式对内部单一的子节点进行动画的管理，在渲染阶段就会往子节点的虚拟`Dom`上挂载一个`transition`属性，表示它的一个被`transition`组件包裹的节点，在`path`阶段就会执行`transition`组件内部钩子，钩子里分为`enter`和`leave`状态，在这个被包裹的子节点上使用`v-if`或`v-show`进行状态的切换。
> 你可以使用`Css`也可以使用`JavaScript`钩子，使用`Css`方式时会在`enter/leave`状态内进行`class`类名的添加和删除，用户只需要写出对应类名的动画即可。
> 如果使用`JavaScript`钩子，则也是按照顺序的执行指定的函数，而这些函数也是需要用户自己定义，组件只是控制这个的流程而已。