---
title: vue3 双向绑定
tag:
  - vue3
  - 数据双向绑定
categories:
  - vue3
  - 双向绑定
abbrlink: 82750b40
date: 2022-09-27 11:46:29
---

[TOC]


## 写在前面

本文的目标是实现一个基本的`vue3`的响应式，包含最基础的情况的处理，本文是系列文章，如果你对`vue3`还不了解，那么请移步：

[超详细整理vue3基础知识💥](https://juejin.cn/post/7102217959669497887)

[手写mini-vue3第三弹！万字实现渲染器首次渲染流程 🎉 🎉 ](https://juejin.cn/post/7142694451448643591)

[更新！更新！实现vue3虚拟DOM更新&diff算法优化🎉 🎉](https://juejin.cn/post/7147318515056410638/)

**本文你将学到**

- 一个基础的响应式实现 ✅
- Proxy ✅
- Reflect ✅
- 嵌套effect的实现 ✅
- computed ✅
- watch ✅
- 浅响应与深响应 ✅
- 浅只读与深只读 ✅
- 处理数组长度 ✅
- ref ✅
- toRefs ✅

![响应式.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/5569070d900a49f69ce692137b20b35d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

## 一. 实现一个完善的响应式

所谓的响应式数据的概念，其实最主要的目的就是**为数据绑定执行函数，当数据发生变动的时候，再次触发函数的执行。**

例如我们有一个对象`data`，我们想让它变成一个响应式数据，当`data`的数据发生变化时，自动执行`effect`函数，使`nextVal`变量的值也进行变化：

```js
// 定义一个对象
let data = {
  name: 'pino',
  age: 18
}

let nextVal
// 待绑定函数
function effect() {
  nextVal = data.age + 1
}

data.age++

```

上面的例子中我们将`data`中的`age`的值进行变化，但是`effect`函数并没有执行，因为现在`effect`函数与`data`这个对象不能说是没啥联系，简直就是半毛钱的关系都没有。

那么怎么才能使这两个毫不相关的函数与对象之间产生关联呢？

因为一个对象最好可以绑定多个函数，所以有没有可能我们为`data`这个对象定义一个空间，每当`data`的值进行变化的时候就会执行这个空间里的函数？

答案是有的。

### 1. Object.defineProperty()

js在原生提供了一个用于操作对象的比较底层的api：`Object.defineProperty()`，它赋予了我们对一个对象的读取和拦截的操作。

`Object.defineProperty()`方法直接在一个对象上定义一个新属性，或者修改一个已经存在的属性， 并返回这个对象。

```js
  Object.defineProperty(obj, prop, descriptor)

```

**参数**

`obj` 需要定义属性的对象。 `prop` 需被定义或修改的属性名。 `descriptor` (描述符) 需被定义或修改的属性的描述符。

其中`descriptor`接受一个对象，对象中可以定义以下的属性描述符，使用属性描述符对一个对象进行拦截和控制：

- `value`——当试图获取属性时所返回的值。
- `writable`——该属性是否可写。
- `enumerable`——该属性在for in循环中是否会被枚举。
- `configurable`——该属性是否可被删除。
- `set()`——该属性的更新操作所调用的函数。
- `get()`——获取属性值时所调用的函数。

另外，数据描述符（其中属性为： `enumerable` ， `configurable` ， `value` ， `writable` ）与存取描述符（其中属性为 `enumerable` ， `configurable` ， `set()` ， `get()` ）之间是有互斥关系的。在定义了 `set()` 和 `get()` 之后，描述符会认为存取操作已被 定义了，其中再定义 `value` 和 `writable` 会引起错误。

```js
 let obj = {
   name: "小花"
 }

 Object.defineProperty(obj, 'name', {
   // 属性读取时进行拦截
   get() { return '小明'; },
   // 属性设置时拦截
   set(newValue) { obj.name = newValue; },
   enumerable: true,
   configurable: true
 });

```

上面的例子中就已经完成对一个对象的最基本的拦截，这也是`vue2.x`中对对象监听的方式，但是由于`Object.defineProperty()`中存在一些问题，例如：

1. 一次只能对一个属性进行监听，需要遍历来对所有属性监听
2. 对于对象的新增属性，需要手动监听
3. 对于数组通过`push`、`unshift`方法增加的元素，也无法监听

那么`vue3`版本中是如何对一个对象进行拦截的呢？答案是`es6`中的`Proxy`。

由于本文主要是`vue3`版本的响应式的实现，如果想要深入了解`Object.defineProperty()`，请移步：

[MDN Object.defineProperty](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FObject%2FdefineProperty)

### 2. Proxy

`proxy`是`es6`版本出现的一种对对象的操作方式，`Proxy` 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。`Proxy` 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

通过`proxy`我们可以实现对一个对象的读取，设置等等操作进行拦截，而且直接对对象进行整体拦截，内部提供了多达13种拦截方式。

- **get(target, propKey, receiver)** ：拦截对象属性的读取，比如 `proxy.foo` 和 `proxy['foo']` 。
- **set(target, propKey, value, receiver)** ：拦截对象属性的设置，比如 `proxy.foo = v` 或 `proxy['foo'] = v` ，返回一个布尔值。
- **has(target, propKey)** ：拦截 `propKey in proxy` 的操作，返回一个布尔值。
- **deleteProperty(target, propKey)** ：拦截 `delete proxy[propKey]` 的操作，返回一个布尔值。
- **ownKeys(target)** ：拦截 `Object.getOwnPropertyNames(proxy)` 、 `Object.getOwnPropertySymbols(proxy)` 、 `Object.keys(proxy)` 、 `for...in` 循环，返回一个数组。该方法返回目标对象所有自身的属性的属性名，而 `Object.keys()` 的返回结果仅包括目标对象自身的可遍历属性。
- **getOwnPropertyDescriptor(target, propKey)** ：拦截 `Object.getOwnPropertyDescriptor(proxy, propKey)` ，返回属性的描述对象。
- **defineProperty(target, propKey, propDesc)** ：拦截 `Object.defineProperty(proxy, propKey, propDesc）` 、 `Object.defineProperties(proxy, propDescs)` ，返回一个布尔值。
- **preventExtensions(target)** ：拦截 `Object.preventExtensions(proxy)` ，返回一个布尔值。
- **getPrototypeOf(target)** ：拦截 `Object.getPrototypeOf(proxy)` ，返回一个对象。
- **isExtensible(target)** ：拦截 `Object.isExtensible(proxy)` ，返回一个布尔值。
- **setPrototypeOf(target, proto)** ：拦截 `Object.setPrototypeOf(proxy, proto)` ，返回一个布尔值。如果目标对象是函数，那么还有两种额外操作可以拦截。
- **apply(target, object, args)** ：拦截 Proxy (代理) 实例作为函数调用的操作，比如 `proxy(...args)` 、 `proxy.call(object, ...args)` 、 `proxy.apply(...)` 。
- **construct(target, args)** ：拦截 Proxy (代理) 实例作为构造函数调用的操作，比如 `new proxy(...args)` 。

如果想要详细了解`proxy`，请移步：

[es6.ruanyifeng.com/#docs/proxy…](https://link.juejin.cn?target=https%3A%2F%2Fes6.ruanyifeng.com%2F%23docs%2Fproxy%23%E6%A6%82%E8%BF%B0)

```js
 let obj = {
   name: "小花"
 }
 // 只使用get和set进行演示
 let obj2 = new Proxy(obj, {
   // 读取拦截
   get: function (target, propKey) {
     return target[propKey]
   },
   // 设置拦截
   set: function (target, propKey, value) {
     // 此处的value为用户设置的新值
     target[propKey] = value
   }
 });

```

### 3. 一个最简单的响应式

有了`proxy`，我们就可以根据之前的思路实现一个基本的响应式功能了，我们的思路是这样的：**在对象被读取时把函数收集到一个“仓库”，在对象的值被设置时触发仓库中的函数。**

由此我们可以写出一个最基本的响应式功能：

```js
// 定义一个“仓库”，用于存储触发函数
let store = new Set()
// 使用proxy进行代理
let data_proxy = new Proxy(data, {
  // 拦截读取操作
  get(target, key) {
    // 收集依赖函数
    store.add(effect)
    return target[key]
  },
  // 拦截设置操作
  set(target, key, newVal) {
    target[key] = newVal
    // 取出所有的依赖函数，执行
    store.forEach(fn => fn())
  }
})

```

我们创建了一个用于保存依赖函数的“仓库”，它是`Set`类型，然后使用`proxy`对对象`data`进行代理，设置了`set`和`get`拦截函数，用于拦截读取和设置操作，当读取属性时，将依赖函数`effect`存储到“仓库”中，当设置属性值时，将依赖函数从“仓库”中取出并重新执行。

还有一个小问题，怎么触发对象的读取操作呢？我们可以直接调用一次`effect`函数，如果在`effect`函数中存在需要收集的属性，那么执行一次`effect`函数也是比较符合常理的。

```scss
// 定义一个对象
let data = {
  name: 'pino',
  age: 18
}

let nextVal
// 待绑定函数
function effect() {
  // 依赖函数在这里被收集
  // 当调用data.age时，effect函数被收集到“仓库”中
  nextVal = data.age + 1
  console.log(nextVal)
}
// 执行依赖函数
effect() // 19

setTimeout(()=>{
  // 使用proxy进行代理后，使用代理后的对象名
  // 触发设置操作，此时会取出effect函数进行执行
  data_proxy.age++ // 2秒后输出 20
}, 2000)

```

一开始会执行一次`effect`，然后函数两秒钟后会执行代理对象设置操作，再次执行`effect`函数，输出20。

![Jul-24-2022 17-31-39.gif](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/256dcd81870a4859b13719ebce24412d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

此时整个响应式流程的功能是这样的：

阶段一，在属性被读取时，为对象属性收集依赖函数：

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b5a6f1e8b1a94060878cee2cb388ee10~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

阶段二，当属性发生改变时，再次触发依赖函数

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a9f81765c42f4943a82d00a17e27cee5~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

这样就实现了一个最基本的响应式的功能。

### 4. 完善

**问题一**

其实上面实现的功能还有很大的缺陷，首先最明显的问题是，我们把`effect`函数给固定了，如果用户使用的依赖函数不叫`effect`怎么办，显然我们的功能就不能正常运行了。

所以先来进行第一步的优化：**抽离出一个公共方法，依赖函数由用户来传递参数**。

我们使用`effect`函数来接受用户传递的依赖函数：

```js
// effect接受一个函数，把这个匿名函数当作依赖函数
function effect(fn) {
  // 执行依赖函数
  fn()
}

// 使用
effect(()=>{
  nextVal = data.age + 1
  console.log(nextVal)
})

```

但是`effect`函数内部只是执行了，在`get`函数中怎么能知道用户传递的依赖函数是什么呢，这两个操作并不在一个函数内啊？其实可以使用一个全局变量`activeEffect`来保存当前正在处理的依赖函数。

修改后的`effect`函数是这样的：

```js
let activeEffect // 新增

function effect(fn) {
  // 保存到全局变量activeEffect
  activeEffect = fn // 新增
  // 执行依赖函数
  fn()
}

// 而在get内部只需要�收集activeEffect即可
get(target, key) {
  store.add(activeEffect)
  return target[key]
},

```

调用`effect`函数传递一个匿名函数作为依赖函数，当执行时，首先会把匿名函数赋值给全局变量`activeEffect`，然后触发属性的读取操作，进而触发`get`拦截，将全局变量`activeEffect`进行收集。

**问题二**

从上面我们定义的对象可以看到，我们的对象`data`中有两个属性，上面的例子中我们只给`age`建立了响应式连接，那么如果我现在也想给`name`建立响应式连接怎么办呢？那好说，那我们直接向“仓库”中继续添加依赖函数不就行了吗。

其实这会带来很严重的问题，由于 **“仓库”并没有与被操作的目标属性之间建立联系**，而上面我们的实现只是将整个“仓库”遍历了一遍，所以无论哪个属性被触发，都会将“仓库”中所有的依赖函数都取出来执行一遍，因为整个执行程序中可能有很多对象及属性都设置了响应式联系，这将会带来很大的性能浪费。所谓牵一发而动全身，这种结果显然不是我们想要的。

```js
let data = {
  name: 'pino',
  age: 18
}

```

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4381410ee6d1419aa556655569cf4047~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

所以我们要重新设计一下“仓库”的数据结构，目的就是为了可以在属性这个粒度下和“仓库”建立明确的联系。

就拿我们上面进行操作的对象来说，存在着两层的结构，有两个角色，对象`data`以及属性`name``age`

```js
let data = {
 name: 'pino',
 age: 18
}

```

他们的关系是这样的：

```js
data
       -> name
               -> effectFn

// 如果两个属性读取了同一个依赖函数
data
       -> name
               -> effectFn
       -> age
               -> effectFn

// 如果两个属性读取了不同的依赖函数
data
       -> name
               -> effectFn
       -> age
               -> effectFn1

// 如果是两个不同的对象
data
       -> name
               -> effectFn
       -> age
               -> effectFn1
data2
       -> addr
               -> effectFn

```

接下来我们实现一下代码，为了方便调用，将设置响应式数据的操作封装为一个函数`reactive`：

```js
let newObj = new Proxy(obj, {
  // 读取拦截
  get: function (target, propKey) {
  },
  // 设置拦截
  set: function (target, propKey, value) {
  }
});

// 封装为

function reactive(obj) {
  return new Proxy(obj, {
    // 读取拦截
    get: function (target, propKey) {
    },
    // 设置拦截
    set: function (target, propKey, value) {
    }
  });
}

function reactive(obj) {
  return new Proxy(obj, {
    get(target, key) {
      // 收集依赖
      track(target, key)
      return target[key]
    },
    set(target, key, newVal) {
      target[key] = newVal
      // 触发依赖
      trigger(target, key)
    }
  })
}

function track(target, key) {
  // 如果没有依赖函数，则不需要进行收集。直接return
  if (!activeEffect) return

  // 获取target，也就是对象名，对应上面例子中的data
  let depsMap = store.get(target)
  if (!depsMap) {
    store.set(target, (depsMap = new Map()))
  }
  // 获取对象中的key值，对应上面例子中的name或age
  let deps = depsMap.get(key)

  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  // 收集依赖函数
  deps.add(activeEffect)
}

function trigger(target, key) {
  // 取出对象对应的Map
  let depsMap = store.get(target)
  if(!depsMap) return
  // 取出key所对应的Set
  let deps = depsMap.get(key)
  // 执行依赖函数
  deps && deps.forEach(fn => fn());
}

```

我们将读取操作封装为了函数`track`，触发依赖函数的动作封装为了`trigger`方便调用，现在的整个“仓库”结构是这样的：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d7c830761e764e60bdda99b8b685844f~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

#### WeakMap

可能有人会问了，为什么设置“仓库”要使用`WeakMap`呢，我使用一个普通对象来创建不行吗？ -

`WeakMap` 结构与 `Map` 结构类似，也是用于生成键值对的集合。

`WeakMap` 与 `Map` 的区别有两点。

首先， `WeakMap` 只接受对象作为键名（ `null` 除外），不接受其他类型的值作为键名。

```js
const map = new WeakMap();
map.set(1, 2)
// TypeError: 1 is not an object!
map.set(Symbol(), 2)
// TypeError: Invalid value used as weak map key
map.set(null, 2)
// TypeError: Invalid value used as weak map key

```

上面代码中，如果将数值 `1` 和 `Symbol` 值作为 WeakMap 的键名，都会报错。

其次， `WeakMap` 的键名所指向的对象，不计入垃圾回收机制。

`WeakMap` 的设计目的在于，有时我们想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。请看下面的例子。

```js
const e1 = document.getElementById('foo');
const e2 = document.getElementById('bar');
const arr = [
    [e1, 'foo 元素'],
    [e2, 'bar 元素'],
];

```

上面代码中， `e1` 和 `e2` 是两个对象，我们通过 `arr` 数组对这两个对象添加一些文字说明。这就形成了 `arr` 对 `e1` 和 `e2` 的引用。

一旦不再需要这两个对象，我们就必须手动删除这个引用，否则垃圾回收机制就不会释放 `e1` 和 `e2` 占用的内存。

```js
// 不需要 e1 和 e2 的时候
// 必须手动删除引用
arr [0] = null;
arr [1] = null;

```

上面这样的写法显然很不方便。一旦忘了写，就会造成内存泄露。

它的键名所引用的对象都是弱引用，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，`WeakMap` 里面的键名对象和所对应的键值对会自动消失，不用手动删除引用。

如果我们上文中`target`对象没有任何引用了，那么说明用户已经不需要用到它了，这时垃圾回收器会自动执行回收，而如果使用`Map`来进行收集，那么即使其他地方的代码已经对`target`没有任何引用，这个`target`也不会被回收。

#### Reflect

在vue3中的实现方式和我们的基本实现还有一点不同就是在vue3中是使用`Reflect`来操作数据的，例如：

```js
function reactive(obj) {
 return new Proxy(obj, {
   get(target, key, receiver) {
     track(target, key)
     // 使用Reflect.get操作读取数据
     return Reflect.get(target, key, receiver)
   },
   set(target, key, value, receiver) {
     trigger(target, key)
     // 使用Reflect.set来操作触发数据
     Reflect.set(target, key, value, receiver)
   }
 })
}

```

那么为什么要使用`Reflect`来操作数据呢，像之前一样直接操作原对象不行吗，我们先来看一下一种特殊的情况：

```js
const obj = {
  foo: 1,
  get bar() {
    return this.foo
  }
}

```

在`effect`依赖函数中通过代理对象p访问bar属性：

```js
effect(()=>{
  console.log(p.bar) // 1
})

```

可以分析一下这个过程发生了什么，当`effect`函数被调用时，会读取`p.bar`属性，他发现`p.bar`属性是一个访问器属性，因此会执行`getter`函数，由于在`getter`函数中通过`this.foo`读取了`foo`属性的值，因此我们会认为副作用函数与属性`foo`之间也会建立联系，当修改`p.foo`的值的时候因该也能够触发响应，使依赖函数重新执行才对，然而当修改`p.foo`的时候，并没有触发依赖函数：

```js
p.foo++

```

实际上问题就出在`bar`属性中的访问器函数`getter`上：

```js
get bar() {
  // 这个this究竟指向谁？
  return this.foo
}

```

当通过代理对象p访问`p.bar`，这回触发代理对象的`get`拦截函数执行：

```js
const p = new Proxt(obj, {
  get(target, key) {
    track(target, key)
    return target[key]
  }
})

```

可以看到在`get`的拦截函数中，通过`target[key]`返回属性值，其中`target`是原始对象`obj`，而`key`就是字符串`'bar'`，所以`target[key]`就相当于`obj.bar`。因此当我们使用`p.bar`访问`bar`属性时，他的`getter`函数内的`this`其实指向原始对象`obj`，这说明我们最终访问的是`obj.foo`。所以在依赖函数内部通过原始对象访问他的某个属性是不会建立响应联系的：

```js
effect(()=>{
  // obj是原始数据，不是代理对象，不会建立响应联系
  obj.foo
})

```

那么怎么解决这个问题呢，这时候就需要用到 `Reflect`出场了。

先来看一下`Reflect`是啥：

`Reflect`函数的功能就是提供了访问一个对象属性的默认行为，例如下面两个操作是等价的：

```js
const obj = { foo: 1 }

// 直接读取
console.log(obj.foo) //1

// 使用Reflect.get读取
console.log(Reflect.get(obj, 'foo')) // 1

```

实际上`Reflect.get`函数还能接受第三个函数，即制定接受者`receiver`，可以把它理解为函数调用过程中的`this`：

```js
const obj = { foo: 1 }

console.log(Reflect.get(obj, 'foo', { foo: 2 })) // 输出的是 2 而不是 1

```

在这段代码中，指定了第三个参数receiver为一个对象`{ foo: 2 }`，这是读取到的值时`receiver`对象的`foo`属性。

而我们上文中的问题的解决方法就是在操作对象数据的时候通过`Reflect`的方法来传递第三个参数`receiver`，它代表谁在读取属性：

```js
const p = new Proxt(obj, {
  // 读取属性接收receiver
  get(target, key, receiver) {
    track(target, key)
    // 使用Reflect.get返回读取到的属性值
    return Reflect.get(target, key, receiver)
  }
})

```

当使用代理对象`p`访问`bar`属性时，那么`receiver`就是p，可以把它理解为函数调用中的`this`。

所以我们改造一下`reactive`函数的实现：

```js
function reactive(obj) {
 return new Proxy(obj, {
   get(target, key, receiver) {
     track(target, key)
     return Reflect.get(target, key, receiver)
   },
   set(target, key, value, receiver) {
     trigger(target, key)
     Reflect.set(target, key, value, receiver)
   }
 })
}

```

**扩展**

**Proxy -> get()**

`get` 方法用于拦截某个属性的读取操作，可以接受三个参数，依次为目标对象、属性名和 `proxy` (代理) 实例本身（严格地说，是操作行为所针对的对象），其中最后一个参数可选。

**Reflect.get(target, name, receiver)**

`Reflect.get` 方法查找并返回 `target` 对象的 `name` 属性，如果没有该属性，则返回 `undefined` 。

```js
var myObject = {
foo: 1,
bar: 2,
get baz() {
  return this.foo + this.bar;
},
}

Reflect.get(myObject, 'foo') // 1
Reflect.get(myObject, 'bar') // 2
Reflect.get(myObject, 'baz') // 3

```

如果 `name` 属性部署了读取函数（ getter ），则读取函数的 `this` 绑定 `receiver` 。

```js
var myObject = {
foo: 1,
bar: 2,
get baz() {
  return this.foo + this.bar;
},
};

var myReceiverObject = {
foo: 4,
bar: 4,
};

Reflect.get(myObject, 'baz', myReceiverObject) // 8

```

如果第一个参数不是对象， `Reflect.get` 方法会报错。

```js
Reflect.get(1, 'foo') // 报错
Reflect.get(false, 'foo') // 报错

```

**Reflect.set(target, name, value, receiver)**

`Reflect.set` 方法设置 `target` 对象的 `name` 属性等于 `value` 。

```js
var myObject = {
foo: 1,
set bar(value) {
  return this.foo = value;
},
}

myObject.foo // 1

Reflect.set(myObject, 'foo', 2);
myObject.foo // 2

Reflect.set(myObject, 'bar', 3)
myObject.foo // 3

```

如果 `name` 属性设置了赋值函数，则赋值函数的 `this` 绑定 `receiver` 。

```js
var myObject = {
foo: 4,
set bar(value) {
  return this.foo = value;
},
};

var myReceiverObject = {
foo: 0,
};

Reflect.set(myObject, 'bar', 1, myReceiverObject);
myObject.foo // 4
myReceiverObject.foo // 1

```

注意，如果 `Proxy` 对象和 `Reflect` 对象联合使用，前者拦截赋值操作，后者完成赋值的默认行为，而且传入了 `receiver` ，那么 `Reflect.set` 会触发 `Proxy.defineProperty` 拦截。

```js
let p = {
a: 'a'
};

let handler = {
set(target, key, value, receiver) {
  console.log('set');
  Reflect.set(target, key, value, receiver)
},
defineProperty(target, key, attribute) {
  console.log('defineProperty');
  Reflect.defineProperty(target, key, attribute);
}
};

let obj = new Proxy(p, handler);
obj.a = 'A';
// set
// defineProperty

```

上面代码中， `Proxy.set` 拦截里面使用了 `Reflect.set` ，而且传入了 `receiver` ，导致触发 `Proxy.defineProperty` 拦截。这是因为 `Proxy.set` 的 `receiver` 参数总是指向当前的 `Proxy` 实例（即上例的 `obj` ），而 `Reflect.set` 一旦传入 `receiver` ，就会将属性赋值到 `receiver` 上面（即 `obj` ），导致触发 `defineProperty` 拦截。如果 `Reflect.set` 没有传入 `receiver` ，那么就不会触发 `defineProperty` 拦截。

```js
let p = {
a: 'a'
};

let handler = {
set(target, key, value, receiver) {
  console.log('set');
  Reflect.set(target, key, value)
},
defineProperty(target, key, attribute) {
  console.log('defineProperty');
  Reflect.defineProperty(target, key, attribute);
}
};

let obj = new Proxy(p, handler);
obj.a = 'A';
// set

```

如果第一个参数不是对象， `Reflect.set` 会报错。

```js
Reflect.set(1, 'foo', {}) // 报错
Reflect.set(false, 'foo', {}) // 报错

```

到这里，一个非常基本的响应式的功能就完成了，整体代码如下：

```js
// 定义仓库
let store = new WeakMap()
// 定义当前处理的依赖函数
let activeEffect

function effect(fn) {
  // 将操作包装为一个函数
  const effectFn = ()=> {
    activeEffect = effectFn
    fn()
  }
  effectFn()
}

function reactive(obj) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      // 收集依赖
      track(target, key)
      return Reflect.get(target, key, receiver)

    },
    set(target, key, newVal, receiver) {
      // 触发依赖
      trigger(target, key)
      Reflect.set(target, key, newVal, receiver)
    }
  })
}

function track(target, key) {
  // 如果没有依赖函数，则不需要进行收集。直接return
  if (!activeEffect) return

  // 获取target，也就是对象名
  let depsMap = store.get(target)
  if (!depsMap) {
    store.set(target, (depsMap = new Map()))
  }
  // 获取对象中的key值
  let deps = depsMap.get(key)

  if (!deps) {
    depsMap.set(key, (deps = new Set()))
  }
  // 收集依赖函数
  deps.add(activeEffect)
}

function trigger(target, key) {
  // 取出对象对应的Map
  let depsMap = store.get(target)
  if (!depsMap) return
  // 取出key所对应的Set
  const effects = depsMap.get(key)
  // 执行依赖函数
  // 为避免污染，创建一个新的Set来进行执行依赖函数
  let effectsToRun = new Set()

  effects && effects.forEach(effectFn => {
      effectsToRun.add(effectFn)
  })

  effectsToRun.forEach(effect => effect())
}

```

## 二. 嵌套effect

在日常的工作中，`effect`函数并不是单独存在的，比如在vue的渲染函数中，各个组件之间互相嵌套，那么他们在组件中所使用的`effect`是必然会发生嵌套的：

```js
effect(function effectFn1() {
  effect(function effectFn1() {
    // ...
  })
})

```

当组件中发生嵌套时，此时的渲染函数：

```js
effect(()=>{
  Father.render()

  //嵌套子组件
  effect(()=>{
    Son.render()
  })
})

```

但是此时我们实现的`effect`并没有这个能力，执行下面这段代码，并不会出现意料之中的行为：

```js
const data = { foo: 'pino', bar: '在干啥' }
// 创建代理对象
const obj = reactive(data)

let p1, p2;
// 设置obj.foo的依赖函数
effect(function effect1(){
  console.log('effect1执行');
  // 嵌套，obj.bar的依赖函数
  effect(function effect2(){
    p2 = obj.bar

    console.log('effect2执行')
  })
  p1 = obj.foo
})

```

在这段代码中，定义了代理对象`obj`，里面有两个属性`foo`和`bar`，然后定义了收集`foo`的依赖函数，在依赖函数的内部又定义了`bar`的依赖函数。 在理想状态下，我们希望依赖函数与属性之间的关系如下：

```rust
obj
        -> foo
                -> effect1
        -> bar
                -> effect2

```

当修改`obj.foo`的值的时候，会触发`effect1`函数执行，由于`effect2`函数在`effect`函数内部，所以`effect2`函数也会执行，而当修改`obj.bar`时，只会触发`effect2`函数。接下来修改一下`obj.foo`：

```js
const data = { foo: 'pino', bar: '在干啥' }
// 创建代理对象
const obj = reactive(data)

let p1, p2;
// 设置obj.foo的依赖函数
effect(function effect1(){
  console.log('effect1执行');
  // 嵌套，obj.bar的依赖函数
  effect(function effect2(){
    p2 = obj.bar

    console.log('effect2执行')
  })
  p1 = obj.foo
})

// 修改obj.foo的值
obj.foo = '前来买瓜'

```

看一下执行结果：

![image_1659170045716_0.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/11ff0565e88140af8381ea563200ca2e~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

可以看到`effect2`函数竟然执行了两次？按照之前的分析，当`obj.foo`被修改后，应当触发`effect1`这个依赖函数，但是为什么会`effect2`会被再次执行呢？ 来看一下我们`effect`函数的实现：

```js
function effect(fn) {
  // 将依赖函数进行包装
  const effectFn = ()=> {
    activeEffect = effectFn
    fn()
  }
  effectFn()
}

```

其实在这里就已经很容易看出问题了，在接受用户传递过来的值时，我们直接将`activeEffect`这个全局变量进行了覆盖！所以在内部执行完后，`activeEffect`这个变量就已经是`effect2`函数了，而且永远不会再次变为`effect1`，此时再进行收集依赖函数时，永远收集的都是`effect2`函数。

那么如何解决这种问题呢，这种情况可以借鉴栈结构来进行处理，栈结构是一种后进先出的结构，在依赖函数执行时，将当前的依赖函数压入栈中，等待依赖函数执行完毕后将其从栈中弹出，始终`activeEffect`指向栈顶的依赖函数。

```js
// 增加effect调用栈
const effectStack = [] // 新增

function effect(fn) {
  let effectFn = function () {
    activeEffect = effectFn
    // 入栈
    effectStack.push(effectFn) // 新增
    // 执行函数的时候进行get收集
    fn()
    // 收集完毕后弹出
    effectStack.pop() // 新增
    // 始终指向栈顶
    activeEffect = effectStack[effectStack.length - 1] // 新增
  }

  effectFn()
}

```

![未命名.drawio_1659171750374_0.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e6f218c9b130475dab6befbfb873dede~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

此时两个属性所对应的依赖函数便不会发生错乱了。

## 三. 避免无限循环

如果现在将`effect`函数中传递的依赖函数改一下：

```js
// 定义一个对象
let data = {
  name: 'pino',
  age: 18
}
// 将data更改为响应式对象
let obj = reactive(data)

effect(() => {
  obj.age++
})

```

在这段代码中，我们将代理对象`obj`的`age`属性执行自增操作，但是执行这段代码，却发现竟然栈溢出了？这是怎么回事呢？

![image_1659163246902_0.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b45cf79a802b45bcbf188a57dd228768~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

其实在`effect`中处理依赖函数时，`obj.age++`的操作其实可以看做是这样的：

```js
effect(()=>{
  // 等式右边的操作是先执行了一次读取操作
  obj.age = obj.age + 1
})

```

这段代码的执行流程是这样的：首先读取`obj.foo`的值，这会触发`track`函数进行收集操作，也就是将当前的依赖函数收集到“仓库”中，接着将其加1后再赋值给`obj.foo`，此时会触发`trigger`操作，即把“仓库”中的依赖函数取出并执行。但是此时该依赖函数正在执行中，还没有执行完就要再次开始下一次的执行。就会导致无限的递归调用自己。

解决这个问题，其实只需要在触发函数执行时，判断当前取出的依赖函数是否等于`activeEffect`，就可以避免重复执行同一个依赖函数。

```js
function trigger(target, key) {
  // 取出对象对应的Map
  let depsMap = store.get(target)
  if (!depsMap) return
  // 取出key所对应的Set
  const effects = depsMap.get(key)
  // // 执行依赖函数
  // 因为删除又添加都在同一个deps中，所以会产生无限执行
  let effectsToRun = new Set()

  effects && effects.forEach(effectFn => {
    // 如果trigger出发执行的副作用函数与当前正在执行的副作用函数相同，则不触发执行
    if (effectFn !== activeEffect) {
            effectsToRun.add(effectFn)
    }
  })

  effectsToRun.forEach(effect => effect())
}

```

## 四.computed

`computed`是vue3中的计算属性，它可以根据传入的参数进行响应式的处理：

```js
const plusOne = computed(() => count.value + 1)

```

根据`computed`的用法，我们可以知道它的几个特点：

1. 懒执行，值变化时才会触发
2. 缓存功能，如果值没有变化，就会返回上一次的执行结果 在实现这两个核心功能之前，我们先来改造一下之前实现的`effect`函数。

怎么能使`effect`函数变成懒执行呢，比如计算属性的这种功能，我们不想要他立即执行，而是希望在它需要的时候才执行。

这时候我们可以在`effect`函数中传递第二个参数，一个对象，用来设置一些额外的功能。

```js
function effect(fn, options = {}) { // 修改

  let effectFn = function () {
    activeEffect = effectFn
    effectStack.push(effectFn)
    fn()
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]
  }
  // 只有当非lazy的时候才直接执行
  if(!options.lazy) {
    effectFn()
  }
  // 将依赖函数组为返回值进行返回
  return effectFn // 新增
}

```

这时，如果传递了`lazy`属性，那么该`effect`将不会立即执行，需要手动进行执行：

```js
const effectFn = effect(()=>{
  console.log(obj.foo)
}, { lazy: true })

// 手动执行
effectFn()

```

但是如果我们想要获取手动执行后的值呢，这时只需要在`effect`函数中将其返回即可。

```js
function effect(fn, options = {}) {

  let effectFn = function () {
    activeEffect = effectFn
    effectStack.push(effectFn)
    // 保存返回值
    const res = fn() // 新增
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]

    return res // 新增
  }
  // 只有当非lazy的时候才直接执行
  if(!options.lazy) {
    effectFn()
  }
  // 将依赖函数组为返回值进行返回
  return effectFn
}

```

接下来开始实现`computed`函数：

```js
function computed(getter) {
  // 创建一个可手动调用的依赖函数
  const effectFn = effect(getter, {
    lazy: true
  })
  // 当对象被访问的时候才调用依赖函数
  const obj = {
    get value() {
      return effectFn()
    }
  }

  return obj
}

```

但是此时还做不到对值进行缓存和对比，增加两个变量，一个存储执行的值，另一个为一个开关，表示“是否可以重新执行依赖函数”：

```js
function computed(getter) {
  // 定义value保存执行结果
  // isRun表示是否需要执行依赖函数
  let value, isRun = true; // 新增
  const effectFn = effect(getter, {
    lazy: true
  })

  const obj = {
    get value() {
      // 增加判断，isRun为true时才会重新执行
      if(isRun) {  // 新增
        // 保存执行结果
        value = effectFn() // 新增
        // 执行完毕后再次重置执行开关
        isRun = false // 新增
      }

      return value
    }
  }

  return obj
}

```

但是上面的实现还有一个问题，就是好像`isRun`执行一次后好像永远都不会变成`true`了，我们的本意是在**数据发生变动的时候需要再次触发依赖函数，也就是将isRun变为true**，实现这种效果，需要我们为`options`再传递一个函数，用于用户自定义的*调度执行*。

```js
function effect(fn, options = {}) {

  let effectFn = function () {
    activeEffect = effectFn
    effectStack.push(effectFn)
    const res = fn() 
    effectStack.pop()
    activeEffect = effectStack[effectStack.length - 1]

    return res 
  }
  // 挂载用户自定义的调度执行器
  effectFn.options = options // 新增

  if(!options.lazy) {
    effectFn()
  }
  return effectFn
}

```

接下来需要修改一下`trigger`如果传递了`scheduler`这个函数，那么只执行`scheduler`这个函数而不执行依赖函数：

```js
function trigger(target, key) {
  let depsMap = store.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)
  let effectsToRun = new Set()

  effects && effects.forEach(effectFn => {
    if (effectFn !== activeEffect) {
        effectsToRun.add(effectFn)
    }
  })

  effectsToRun.forEach(effect => {
    // 如果存在调度器scheduler，那么直接调用该调度器，并将依赖函数进行传递
    if(effectFn.options.scheduler) { // 新增
      effectFn.options.scheduler(effect) // 新增
    } else {
      effect()
    }
  })
}

```

那么在`computed`中就可以实现重置执行开关`isRun`的操作了：

```js
function computed(getter) {
  // 定义value保存执行结果
  // isRun表示是否需要执行依赖函数
  let value, isRun = true; // 新增
  const effectFn = effect(getter, {
    lazy: true,
    scheduler() {
      if(!isRun) {
        isRun = true
      }
    }
  })

  const obj = {
    get value() {
      // 增加判断，isRun为true时才会重新执行
      if(isRun) {  // 新增
        // 保存执行结果
        value = effectFn() // 新增
        // 执行完毕后再次重置执行开关
        isRun = false // 新增
      }

      return value
    }
  }

  return obj
}

```

当`computed`传入的依赖函数中的值发生改变时，会触发响应式对象的`trigger`函数，而计算属性创建响应式对象时传入了`scheduler`，所以当数据改变时，只会执行`scheduler`函数，在`scheduler`函数内我们将执行开关重置为`true`，再下次访问数据触发`get`函数时，就会重新执行依赖函数。这也就实现了*当数据发生改变时，会再次触发依赖函数*的功能了。

为了避免计算属性被另外一个依赖函数调用而失去响应，我们还需要为计算属性单独进行绑定响应式的功能，形成一个`effect`嵌套。

```js
function computed(getter) {
  let value, isRun = true; 
  const effectFn = effect(getter, {
    lazy: true,
    scheduler() {
      if(!isRun) {
        isRun = true
        // 当计算属性依赖的响应式数据发生变化时，手动调用trigger函数触发响应
        trigger(obj, 'value') // 新增
      }
    }
  })

  const obj = {
    get value() {
      if(isRun) { 
        value = effectFn()
        isRun = false 
      }
      // 当读取value时，手动调用track函数进行追踪
          track(obj, 'value')
      return value
    }
  }

  return obj
}

```

## 五. watch

先来看一下`watch`函数的用法，它的用法也非常简单：

```js
watch(obj, ()=>{
  console.log(改变了)
})

// 修改数据，触发watch函数
obj.age++

```

`watch`接受两个参数，第一个参数为绑定的响应式数据，第二个参数为依赖函数，我们依然可以沿用之前的思路来进行处理，利用`effect`以及`scheduler`来改变触发执行时机。

```js
function watch(source, fn) {
  effect(
    // 递归读取对象中的每一项，变为响应式数据，绑定依赖函数
        ()=> bindData(source),
    {
      scheduler() {
        // 当数据发生改变时，调用依赖函数
        fn()
      }
    }
  )
}
// readData保存已读取过的数据，防止重复读取
function bindData(value, readData = new Set()) {
  // 此处只考虑对象的情况，如果值已被读取/值不存在/值不为对象，那么直接返回
  if(typeof value !== 'object' || value == null || readData.has(value)) return
  // 保存已读取对象
  readData.add(value)
  // 遍历对象
  for(const key in value) {
    // 递归进行读取
    bindData(value[key], readData)
  }
  return value
}

```

`watch`函数还有另外一种用法，就是除了接收对象，还可以接受一个`getter`函数，例如：

```js
watch(
    ()=> obj.age,
    ()=> {
      console.log('改变了')
    } 
)

```

这种情况下只需要将用户传入的`getter`将我们自定义的`bindData`替代即可：

```js
function watch(source, fn) {
  let getter = typeof source === 'function' ? source : ((）=> bindData(source))

  effect(
    // 执行getter
        ()=> getter(),
    {
      scheduler() {
        // 当数据发生改变时，调用依赖函数
        fn()
      }
    }
  )
}

```

其实`watch`函数还有一个很重要的功能：就是在用户传递的依赖函数中可以获取新值和旧值，但是我们目前还做不到这一点。实现这个功能我们可以配置前文中的`lazy`属性来实现。 来回顾一下`lazy`属性：设置了`lazy`之后一开始不会执行依赖函数，手动执行时会返回执行结果：

```js
function watch(source, fn) {
  let getter = typeof source === 'function' ? source : ((）=> bindData(source))
  // 定义新值与旧值
  let newVal, oldVal; // 新增
  const effectFn = effect(
    // 执行getter
        ()=> getter(),
    {
      lazy: true,
      scheduler() {
        // 在scheduler重新执行依赖函数，得到新值
        newVal = effectFn() // 新增
        fn(newVal, oldVal) // 新增
        // 执行完毕后更新旧值
        oldVal = newVal // 新增
      }
    }
  )
  // 手动调用依赖函数，取得旧值
  oldVal = effectFn() // 新增
}

```

此外，`watch`函数还有一个功能，就是可以自定义执行时机，比如`immediate`属性，他会在创建时立即执行一次：

```js
watch(obj, ()=>{
  console.log('改变了')
}, {
  immediate: true
})

```

我们可以把`scheduler`封装为一个函数，以便在不同的时机去调用他：

```js
function watch(source, fn, options = {}) {
  let getter = typeof source === 'function' ? source : ((）=> bindData(source))

  let newVal, oldVal; 
  const run = () => { // 新增
    newVal = effectFn()
    fn(newVal, oldVal)
    oldVal = newVal
  }
  const effectFn = effect(
        ()=> getter(),
    {
      lazy: true,
      // 使用run来执行依赖函数
      scheduler: run  // 修改
    }
  )
  // 当immediate为true时，立即执行一次依赖函数
  if(options.immediate) { // 新增
    run() // 新增
  } else {
    oldVal = effectFn() 
  }
}

```

`watch`函数还支持其他的执行调用时机，这里只实现了`immediate`。

## 六. 浅响应与深响应

深响应和浅响应的区别：

```js
const obj = reatcive({ foo: { bar: 1} })

effect(()=>{
  console.log(obj.foo.bar)
})

// 修改obj.foo.bar的值，并不能触发响应
obj.foo.bar = 2

```

因为之前实现的拦截，无论对于什么类型的数据都是直接进行返回的，如果实现深响应，那么首先应该判断是否为对象类型的值，如果是对象类型的值，应当递归调用`reactive`方法进行转换。

```js
// 接收第二个参数，标记为是否为浅响应
function createReactive(obj, isShallow = false) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      // 访问raw时，返回原对象
      if(key === 'raw') return target
      track(target, key)

      const res = Reflect.get(target, key, receiver)
      // 如果是浅响应，直接返回值
      if(isShallow) {
        return res
      }
      // 判断res是否为对象并且不为null，循环调用reatcive
      if(typeof res === 'object' && res !== null) {
        return reatcive(res)
      }
      return res
    },
    // ...省略其他
  })

```

将创建响应式对象的方法抽离出去，通过传递`isShallow`参数来决定是否创建深响应/浅响应对象。

```js
// 深响应
function reactive(obj) {
  return createReactive(obj)
}

// 浅响应
function shallowReactive(obj) {
  return createReactive(obj, true)
}

```

## 七. 浅只读与深只读

有时候我们并不需要对值进行修改，也就是需要值为只读的，这个操作也分为深只读和浅只读，首先需要在`createReactive`函数中增加一个参数`isReadOnly`，代表是否为只读属性。

```js
// 浅只读
function shallowReadOnly(obj) {
  return createReactive(obj, true, true)
}

// 深只读
function readOnly(obj) {
  return createReactive(obj, false, true)
}

set(target, key, newValue, receiver) {
  // 是否为只读属性，如果是则打印警告信息并直接返回
  if(isReadOnly) {
    console.log(`属性${key}是只读的`)
    return false
  }

  const oldVal = target[key]
  const type = Object.prototype.hasOwnProperty.call(target, key) ? triggerType.SET : triggerType.ADD
  const res = Reflect.set(target, key, newValue, receiver)
  if (target === receiver.raw) {
    if (oldVal !== newValue && (oldVal === oldVal || newValue === newValue)) {
      trigger(target, key, type)
    }
  }

  return res
}

```

如果为只读属性，那么也不需要为其建立响应联系 如果为只读属性，那么在进行深层次遍历的时候，需要调用`readOnly`函数对值进行包装

```js
function createReactive(obj, isShallow = false, isReadOnly = false) {
  return new Proxy(obj, {
    get(target, key, receiver) {
      // 访问raw时，返回原对象
      if (key === 'raw') return target

      //只有在非只读的时候才需要建立响应联系
      if(!isReadOnly) {
        track(target, key)
      }

      const res = Reflect.get(target, key, receiver)
      // 如果是浅响应，直接返回值
      if (isShallow) {
        return res
      }
      // 判断res是否为对象并且不为null，循环调用creative
      if (typeof res === 'object' && res !== null) {
        // 如果数据为只读，则调用readOnly对值进行包装
        return isReadOnly ? readOnly(res) : creative(res)
      }
      return res
    },
  })
}

```

## 八. 处理数组

### 数组的索引与length

如果操作数组时，设置的索引值大于数组当前的长度，那么要更新数组的`length`属性，所以当通过索引设置元素值时，可能会隐式的修改`length`的属性值，因此再j进行触发响应时，也应该触发与`length`属性相关联的副作用函数重新执行。

```js
const arr = reactive(['foo']) // 数组原来的长度为1

effect(()=>{
  console.log(arr.length) //1
})

// 设置索引为1的值，会导致数组长度变为2
arr[1] = 'bar'

```

在判断操作类型时，新增对数组类型的判断，如果代理目标是数组，那么对于操作类型的判断作出处理：

如果设置的索引值小于数组的长度，就视为`SET`操作，因为他不会改变数组长度，如果设置的索引值大于当前数组的长度，那么应该被视为`ADD`操作。

```js
// 定义常量，便于修改
const triggerType = {
  ADD: 'add',
  SET: 'set'
}

set(target, key, newValue, receiver) {
  if(isReadOnly) {
    console.log(`属性${key}是只读的`)
    return false
  }

  const oldVal = target[key]

  // 如果目标对象是数组，检测被设置的索引值是否小于数组长度
  const type = Array.isArray(target) && (Number(key) > target.length ? triggerType.ADD : triggerType.SET)
  const res = Reflect.set(target, key, newValue, receiver)

  trigger(target, key, type)

  return res
},

function trigger(target, key, type) {
  const depsMap = store.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)
  let effectsToRun = new Set()

  effects && effects.forEach(effectFn => {
    if (effectFn !== activeEffect) {
      effectsToRun.add(effectFn)
    }
  })

  // 当操作类型是ADD并且目标对象时数组时，应该取出执行那些与 length 属性相关的副作用函数
  if(Array.isArray(target) && type === triggerType.ADD) {
    // 取出与length相关的副作用函数
    const lengthEffects = deps.get('length')

    lengthEffects && lengthEffects.forEach(effectFn => {
      if (effectFn !== activeEffect) {
        effectsToRun.add(effectFn)
      }
    })
  }

  effectsToRun.forEach(effect => {
    if (effectFn.options.scheduler) {
      effectFn.options.scheduler(effect)
    } else {
      effect()
    }
  })

}

```

还有一点：其实修改数组的`length`属性也会隐式的影响数组元素：

```js
const arr = reactive(['foo'])

effect(()=>{
  // 访问数组的第0个元素
  console.log(arrr[0]) // foo
})

// 将数组的长度修改为0，导致第0个元素被删除，因此应该触发响应
arr.length = 0

```

如上所示，在副作用函数内部访问了第0个元素，然后将数组的`length`属性修改为0，这回隐式的影响数组元素，及所有的元素都会被删除，所以应该触发副作用函数重新执行。

然而并非所有的对`length`属性值的修改都会影响数组中的已有元素，如果设置的`length`属性为100，这并不会影响第0个元素，**当修改属性值时，只有那些索引值大于等于新的`length`属性值的元素才需要触发响应。**

调用`trigger`函数时传入新值：

```js
set(target, key, newValue, receiver) {
  if(isReadOnly) {
    console.log(`属性${key}是只读的`)
    return false
  }

  const oldVal = target[key]

  // 如果目标对象是数组，检测被设置的索引值是否小于数组长度
  const type = Array.isArray(target) && (Number(key) > target.length ? triggerType.ADD : triggerType.SET)
  const res = Reflect.set(target, key, newValue, receiver)

  // 将新的值进行传递，及触发响应的新值
  trigger(target, key, type, newValue) // 新增

  return res
}

```

判断新的下标值与需要操作的新的下标值进行判断，因为数组的`key`为下标，所以副作用函数搜集器是以下标作为`key`值的，当`length`发生变动时，只需要将新值与每个下标的`key`判断，大于等于新的`length`值的需要重新执行副作用函数。

![未命名绘图.drawio_(2)_1659679803962_0.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1b1e5ceb414c4df389eeff97908ba903~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

如上图所示，`Map`为根据数组的`key`，也就是`id`组成的`Map`结构，他们的每一个`key`都对应一个`Set`，用于保存这个`key`下面的所有的依赖函数。

当`length`属性发生变动时，应当取出所有`key`值大于等于`length`值的所有依赖函数进行执行。

```js
function trigger(target, key, type, newValue) {
  const depsMap = store.get(target)
  if (!depsMap) return
  const effects = depsMap.get(key)
  let effectsToRun = new Set()

  effects && effects.forEach(effectFn => {
    if (effectFn !== activeEffect) {
      effectsToRun.add(effectFn)
    }
  })

  // 如果操作目标是数组，并且修改了数组的length属性
  if(Array.isArray(target) && key === 'length') {
    // 对于索引值大于或等于新的length元素
    // 需要把所有相关联的副作用函数取出并添加到effectToRun中待执行
    depsMap.forEach((effects, key)=>{
      // key 与 newValue均为数组下标，因为数组中key为index
      if(key >= newValue) {
        effects.forEach(effectFn=>{
          if (effectFn !== activeEffect) {
            effectsToRun.add(effectFn)
          }
        })
      }
    })
  }

  // ...省略
}

```

本文的实现数组这种数据结构只考虑了针对长度发生变化的情况。

## 九. ref

由于Proxy的代理目标是非原始值，所以没有任何手段去拦截对原始值的操作：

```js
let str = 'hi'
// 无法拦截对值的修改
str = 'pino'

```

解决方法是：使用一个非原始值去包裹原始值：

```js
function ref(val) {
  // 创建一个对象对原始值进行包裹
  const wrapper = {
    value: val
  }
  // 使用reactive函数将包裹对象编程响应式数据并返回
  return reactive(wrapper)
}

```

如何判断是用户传入的对象还是包裹对象呢？

```js
const ref1 = ref(1)
const ref2 = reactive({ value: 1 })

```

只需要在包裹对象内部定义一个不可枚举且不可写的属性：

```js
function ref(val) {
  // 创建一个对象对原始值进行包裹
  const wrapper = {
    value: val
  }
  // 定义一个属性值__v_isRef，值为true，代表是包裹对象
  Object.defineProperty(wrapper, '_isRef', {
    value: true
  })
  // 使用reactive函数将包裹对象编程响应式数据并返回
  return reactive(wrapper)
}

```

## 十. 响应丢失问题与toRefs

在使用...解构赋值时会导致响应式丢失：

```js
const obj = reactive({ foo: 1, bar: 2 })

// 将响应式数据展开到一个新的对象newObj
const newObj = {
  ...obj
}
// 此时相当于：
const newObj = {
  foo: 1,
  bar: 2
}

effect(()=>{
  //在副作用函数中通过新对象newObj读取foo属性值
  console.log(newObj.foo)
})

// obj,foo并不会触发响应
obj.foo = 100

```

首先创建一个响应式对象obj，然后使用展开运算符得到一个新对象`newObj`，他是一个普通对象，不具有响应式的能力，所以修改`obj.foo`的值不会触发副作用函数重新更新。

解决方法：

```js
const newObj = {
  foo: {
    // 用于返回其原始的响应式对象
    get value() {
      return obj.foo
    }
  },
  bar: {
    get value() {
      return obj.bar
    }
  }
}

```

将单个值包装为一个对象，相当于访问该属性的时候会得到该属性的`getter`，在`getter`中返回原始的响应式对象。

相当于解构访问`newObj.foo` === `obj.foo`。

```js
{
  get value() {
    return obj.foo
  }
}

```

### toRefs

```js
function toRefs(obj) {
  let res = {}
  // 处理整个对象时，将属性依次进行遍历，调用toRef进行转化
  for(let key in obj) {
    res[key] = toRef(obj, key)
  }
  return res
}


function toRef(obj, key) {
  const wrapper = {
    // 允许读取值
    get value() {
      return obj[key]
    },
    // 允许设置值
    set value(val) {
      obj[key] = val
    }
  }
  // 标志为ref对象
  Object.defineProperty(wrapper, '_isRef', {
    value: true
  })
  return wrapper
}

```

使用`toRefs`处理整个对象，在`toRefs`这个函数中循环处理了对象所包含的所有属性。

```js
  const newObj = { ...toRefs(obj) }

```

当设置`value`属性值的时候，最终设置的是响应式数据的同名属性值。

一个基本的`vue3`响应式就完成了，但是本文所实现的依然是阉割版本，有很多情况都没有进行考虑，还有好多功能没有实现，比如：拦截 `Map`，`Set`，数组的其他问题，对象的其他问题，其他api的实现，但是上面的实现已经足够让你理解vue3响应式原理实现的核心了，这里还有很多其他的资料需要推荐，比如阮一峰老师的es6教程，对于vue3底层原理的实现，许多知识依然是需要回顾和复习，查看原始底层的实现，再比如霍春阳老师的《vue.js的设计与实现》这本书，这本书目前我也只看完了一半，但是截止到目前我认为这本书对于学习`vue3`的原理是非常深入浅出，鞭辟入里的，本文的许多例子也是借鉴了这本书。

最后当然是需要取读一读源码，不过在读源码之前能够先了解一下实现的核心原理，再去看源码是事半功倍的。希望大家都能早日学透源码，面试的时候能够对答如流，工作中遇到的问题也能从原理层面去理解和更好地解决！

目前我也在实现一个`mini-vue`，截止到目前只实现了响应式部分，而且与本文的实现方式有所不同，后续还会继续实现编译和虚拟DOM部分，欢迎star！👇

[k-vue](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fkonvyi%2Fk-vue)

如果想学习《vue.js的设计与实现》这本书这本书，那么请关注下面这个链接👇作为参考，里面包含了根据具体的问题的功能进行拆分实现，同样也只实现了响应式的部分！

[vue3-analysis](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fkonvyi%2Fvue3-analysis)

**实现一个mini-vue系列文章**

[超详细整理vue3基础知识💥](https://juejin.cn/post/7102217959669497887)

**写在最后** ⛳

未来可能会更新实现`mini-vue3`和`javascript`基础知识系列，希望能一直坚持下去，期待多多点赞🤗🤗，一起进步！🥳🥳

**本文参考**

[es6.ruanyifeng.com/#docs/proxy](https://link.juejin.cn?target=https%3A%2F%2Fes6.ruanyifeng.com%2F%23docs%2Fproxy)

[developer.mozilla.org/en-US/docs/…](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fen-US%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FObject%2FdefineProperty)

[book.douban.com/subject/357…](https://link.juejin.cn?target=https%3A%2F%2Fbook.douban.com%2Fsubject%2F35768338%2F)

出处
作者：小黄瓜没有刺
链接：https://juejin.cn/post/7129644396533776420
来源：稀土掘金
