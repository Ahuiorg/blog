---
uuid: 49c30ae4-120c-11ed-aacc-b11deac0aae6
title: JS typeof instanceof 你应该知道这么多
abbrlink: 3369490582
date: 2020-03-01 13:05:34
tags: [js,web,浏览器]
categories: [前端基础]
---
### `typeof`主要是用来判断一个变量的类型

<!-- more -->

几个特殊的情况记一下：
```js
typeof null // object
typeof {} // object
typeof [] // object
typeof Object // function
typeof Function // function
typeof undefined // undefined
```
看到上边  `typeof Object` 的结果是 `function` 这里其实因为 Object 是一个构造函数， 而不是一个真正的对象， 只有实例化之后才会给出 `object`的结果

可以看一下下边代码：

```js
console.log(typeof Object); // function

let ahui = new Object();
console.log(typeof ahui); // object
```

再看到上边： `typeof null` `typeof {}` `typeof []` 结果都是 object， 很显然， 我们是不能通过 typeof 来判断一个对象的具体类型的，通常我们会通过 `Object.prototype.toString`的方法来实现

```js
Object.prototype.toString.call(1) // "[object Number]"

Object.prototype.toString.call('hi') // "[object String]"

Object.prototype.toString.call({a:'hi'}) // "[object Object]"

Object.prototype.toString.call([1,'a']) // "[object Array]"

Object.prototype.toString.call(true) // "[object Boolean]"

Object.prototype.toString.call(() => {}) // "[object Function]"

Object.prototype.toString.call(null) // "[object Null]"

Object.prototype.toString.call(undefined) // "[object Undefined]"

Object.prototype.toString.call(Symbol(1)) // "[object Symbol]"
```

到这里， 好像还没说到 typeof 的实现原理，其实很简单，通常情况下，我们只需要知道他的用法以及一些注意点就行：

**JS 要底层存储变量的时候， 会在变量的机器码的低位 1 ~ 3 位存储其类型信息**

### instanceof表示指定对象是否为某个构造函数的实例。

比如下边代码：

```js
const Person = function (name) {
  this.name = name;
}

const ahui = new Person('ahui');
const angeli = {
  name: 'angele'
}

// ahui 是通过 Person 实例出来的
console.log(ahui instanceof Person); // true

// 这个时候突然想起， 一个对象的__proto__ 指向该对象的构造函数的原型，如下：
console.log(ahui.__proto__ === Person.prototype); // true

console.log(angeli instanceof Person); // false
```

当然，instanceof 也可以判断一个实例是否是其父类型或者祖先类型的实例

```js
const Person = function () {}
const Programmer = function () {}

Programmer.prototype = new Person()
const ahui = new Programmer();

console.log(ahui instanceof Programmer); // true
console.log(ahui instanceof Person); // true
```

再看几个有意思的例子：

```js
function Foo() {}

console.log(Object instanceof Object) // true
console.log(Function instanceof Function) // true
console.log(Number instanceof Number); // false 
console.log(String instanceof String); // false 
console.log(Function instanceof Object) // true
console.log(Object instanceof Function) // true

console.log(Foo instanceof Foo) // false
console.log(Foo instanceof Object) // true
console.log(Foo instanceof Function) // true
```

看到上边的 `instanceof` 有没有感觉很有意思，下边让我们来讲一下为什么会这样：

先看一张图片 

<img src="/images/web/js/proto.jpg" align="center" style="margin: 0 auto;">
<pr />

如果看这个图片表示看不懂的， 可以先看一下这篇文章： [js 中`__proto__`和 `prototype` 的区别和关系](/proto.html)

如果有同学对这张图表示怀疑的，就像我，可以先在控制台试一下下边的代码：

```js
Object.__proto__ === Function.prototype; // true
Function.__proto__ === Function.prototype; // true
Function.prototype.__proto__ === Object.prototype; // true
console.log(Object.prototype.__proto__); // null
```

下边我们再来理解 `Object instanceof Object` 为 `true`
```js
// 为了方便表述， 首先区分左侧表达式跟右侧表达式
ObjectL = Object, ObjectR = Object; 
// 下面根据规范逐步推演
O = ObjectR.prototype = Object.prototype 
L = ObjectL.__proto__ = Function.prototype 
// 第一次判断
O != L 
// 循环查找 L 是否还有 __proto__ 
L = Function.prototype.__proto__ = Object.prototype 
// 第二次判断
O == L 
// 返回 true
```
同理， 我们可以通过上边那张 js 原型的关系图推算出 其它所有的 `instanceof` 


### 自己手动实现 `instanceof`

现在， 我们知道 instanceof 的原理了， 那么，自己手动去实现一个 instanceof 是很简单的

```js
function instanceof_myself(L, R) {
  const O = R.prototype;
  L = L.__proto__;

  while(true) {
    if (L === null) {
      return false;
    }
    if (L === O) {
      return true;
    }
    L = L.__proto__;
  }
}

// 测试一下

const Person = function () {}
const ahui = new Person();

console.log(ahui instanceof Person) // true
console.log(instanceof_myself(ahui, Person)); //true

```


参考地址：
[浅谈 instanceof 和 typeof 的实现原理](https://blog.csdn.net/qq_38722097/article/details/80717240)
[JavaScript instanceof 运算符深入剖析](https://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/)
