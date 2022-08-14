---
uuid: 49c30ae2-120c-11ed-aacc-b11deac0aae6
title: JS this
abbrlink: 3915840815
date: 2020-03-01 21:18:26
tags: [js, web, this]
categories: [前端基础]
---
有关于`this` ，我们说得最多的一句话就是**谁调用，指向谁**；也就是

 > this的指向在函数定义的时候是确定不了的，只有函数执行的时候才能确定this到底指向谁，实际上this的最终指向的是那个调用它的对象

<!-- more -->

其实， 在我们 JS 里边，要想真的理解 `this` ，只知道上边这一句话是完全不行的，我们可以从以下几个方面学习跟理解 `this` ：

从 `this` 绑定层面去理解：默认绑定，隐式绑定，显式绑定，`new` 绑定

从函数的运行环境层面去理解: `this`， 内存， 函数，环境变量

能过实际代码的上下文去理解

### 默认绑定

先看一个例子：

```js
var name = "global"

function foo () {
  var name = "foo"
  console.log(this.name);
}

foo();  // global
```

很显然， `foo` 函数执行的时候，所在的环境是 `window`, 所以 `this.name` 就是 `window` 的 `name` 属性

再看一个稍微复杂点的例子：

```js
const name = "global";

function foo() {
  console.log(this.name); // global
  foo.name = "foo"

  function sayname() {
    console.log(this.name); // global
  }
  sayname()
}

foo()
```

上边第一个 `this` 很好理解，是 `foo` 在全局环境下被调用，所以是 `global`

而第二个 `this`，表面上看上去是在 `foo` 函数内部被执行，而且 `foo.name` 赋了新值 `'foo'`, 所以很容易就会认为第二个 `this` 会打印 `foo`， 其实不然；

`sayname` 虽是在 `foo` 函数里边被执行，但并非是被 `foo` 函数调用，所以，调用 `sayname` 的还是 `window`; 

可以参考下边代码进行理解:

```js
const name = "global";

function foo() {
  console.log(this.name); // global
  foo.name = "foo"
  foo.sayname = function () {
    console.log(this.name); // foo
  }

  foo.sayname(); // 这里的 sayname 函数才是被 foo 调用的，所以才会打印 foo
}

foo()
```

这就是默认绑定规则,它是 javascript 中最常见的一种函数调用模式，this 的绑定规则也是四种绑定规则中最简单的一种，就是绑定在全局作用域上。


### 隐式绑定

先看例子： 

```js
function sayname() {
  console.log(this.name);
}

const ahui = {
  name: "ahui",
  sayname: sayname,
}

const angeli = {
  name: "angeli",
  sayname: sayname,
}

ahui.sayname(); // ahui
angeli.sayname(); // angeli

```
这就是隐式绑定，不难理解; 回到我们文章的第一句话，**谁调用，指向谁**，这里就分别是 `ahui` `angeli` 调用了 `sayname`

专业一点的说法就是上下文对象，当给函数指定了这个上下文对象时，函数内部的 `this` 自然指向了这个上下文对象

在上边例子的基础上，我们再看一个稍微复杂点的例子：

```js
function sayname() {
  console.log(this.name);
}

const name = "global";

const ahui = {
  name: "ahui",
  sayname: sayname,
}

ahui.sayname(); // ahui

const ahuisayname = ahui.sayname;
ahuisayname(); // global
```

这里的 ahui.sayname() 很好理解，肯定会打印 ahui ， 但是，当我们把 ahui.sayname 赋给一个新的变量之后， 为啥就变了呢 ？

这就是常见的**隐式绑定时丢失上下文**

让我们来分析一下上边这个赋值语句：**由于在 javascript 中，函数是对象，对象之间是引用传递，而不是值传递。**  所以这个赋值语句可以理解为 `ahuisayname = ahui.sayname = sayname`, 也就是 `ahuisayname = sayname` ,  `ahui.sayname` 只起了一个桥梁的作用, `ahuisayname` 最终引用的就是 `sayname` 函数的地址，而与 `ahui` 这个对象没有关系了。最终执行 `ahuisayname` 这个函数，中不过是简单的执行 `sayname` 这个函数，输出 `'global'`。

这里的详细分析，可以看一下这篇文章： [JavaScript 的 this 原理](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)


### 显示绑定

js中提示显示绑定的方法有3个 `call` `apply` `bind`

`call` `apply` 用法基本相似，就是给函数绑定一个执行上下文，且是显式绑定的。因此，函数内的this自然而然的绑定在了 `call` 或者 `apply` 所调用的对象上面。

> `apply(obj,[arg1,arg2,arg3,...]` 被调用函数的参数以数组的形式给出
> `call(obj,arg1,arg2,arg3,...)` 被调用函数的参数依次给出

```js
// 带参数
function count(num1, num2) {
    console.log(this.a * num1 + num2)
}

var obj1 = {
    a: 2
}
var obj2 = {
    a: 3
}

count.call(obj1, 1, 2) // 4
count.apply(obj1, [1, 2]) // 4

count.call(obj2, 1, 2) // 5
count.apply(obj2, [1, 2]) // 5
```

`bind` 方法只是返回了一个新的函数，这个函数内的 `this` 指定了执行上下文，而返回这个新函数可以接受参数

```js
// 带参数
function count(num1, num2) {
    console.log(this.a * num1 + num2)
}

var obj1 = {
    a: 2
}

var bound1 = count.bind(obj1) // 未指定参数
bound1(1, 2) // 4

var bound2 = count.bind(obj1, 1) // 指定了一个参数
bound2(2) // 4 

var bound3 = count.bind(obj1, 1, 2) // 指定了两个参数
bound3() //4

var bound4 = count.bind(obj1, 1, 2, 3) // 指定了多余的参数,多余的参数会被忽略
bound4() // 4
```

### new 绑定

最后要讲的一种 `this` 绑定规则，是指通过 `new` 操作符调用构造函数时发生的 `this` 绑定。首先要明确一点的是，在 javascript 中并没有其他语言那样的类的概念。构造函数也仅仅是普通的函数而已，只不过构造函数的函数名以大写字母开头，也只不过它可以通过 `new` 操作符调用而已.

```js
function Person(name,age) {
    this.name = name
    this.age = age
    console.log("我也只不过是个普通函数")
}
Person("ahui",18) // "我也只不过是个普通函数"
console.log(name) // "ahui"
console.log(age) // 18

var zxt = new Person("angeli",22) // "我也只不过是个普通函数"
console.log(zxt.name) // "angeli"
console.log(zxt.age) // 22
```

上面这个例子中，首先定义了一个 `Person` 函数，既可以普通调用，也可以以构造函数的形式的调用。

当普通调用时，则按照正常的函数执行，输出一个字符串。 

如果是通过一个 `new` 操作符,则构造了一个新的对象。

那么，接下来我们再看看两种调用方式， `this` 分别绑定在了何处首先普通调用时，前面已经介绍过，此时应用默认绑定规则，`this` 绑定在了全局对象上，此时全局对象上会分别增加 name 和 age 两个属性。当通过 `new` 操作符调用时，函数会返回一个对象，从输出结果上来看 `this` 对象绑定在了这个返回的对象上。

因此，所谓的 `new` 绑定是指通过 `new` 操作符来调用函数时，会产生一个新对象，并且会把构造函数内的 `this` 绑定到这个对象上。

事实上，在javascript中，使用 `new` 来调用函数，会自动执行下面的操作。详情可看这里： [new 一个对象的过程](/3315289936.html)

> 1. 创建一个全新的对象
> 2. 这个新对象会被执行原型连接
> 3. 这个新对象会绑定到函数调用的this
> 4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象


### 四种绑定的优先级

上面讲述了javascript中四种this绑定规则，这四种绑定规则基本上涵盖了所有函数调用情况。但是如果同时应用了这四种规则中的两种甚至更多，又该是怎么样的一个情况，或者说这四种绑定的优先级顺序又是怎么样的。

首先，很容易理解，默认绑定的优先级是最低的。这是因为只有在无法应用其他this绑定规则的情况下，才会调用默认绑定。那隐式绑定和显式绑定呢？还是上代码吧，代码可从来不会说谎。

```js
function speak() {
    console.log(this.name)
}

var obj1 = {
    name: 'obj1',
    speak: speak
}
var obj2 = {
    name: 'obj2'
}

obj1.speak() // obj1 (1)
obj1.speak.call(obj2) // obj2 (2)
```
所以在上面代码中，执行了obj1.speak(),speak函数内部的this指向了obj1，因此(1)处代码输出的当然就是obj1，但是当显式绑定了speak函数内的this到obj2上，输出结果就变成了obj2，所有从这个结果可以看出显式绑定的优先级是要高于隐式绑定的。

事实上我们可以这么理解obj1.speak.call(obj2)这行代码，obj1.speak只是间接获得了speak函数的引用，这就有点像前面所说的隐式绑定丢失了上下文。

好，既然显式绑定的优先级要高于隐式绑定，那么接下来再来比较一下new 绑定和显式绑定。

```js

function foo(something) {
    this.a = something
}

var obj1 = {}
var bar = foo.bind(obj1)  // 返回一个新函数bar，这个新函数内的this指向了obj1  (1)
bar(2) // this绑定在了Obj1上，所以obj1.a === 2
console.log(obj1.a)

var baz = new bar(3)  // 调用new 操作符后，bar函数的this指向了返回的新实例baz  (2)

console.log(obj1.a)
console.log(baz.a) 
```

我们可以看到，在(1)处，bar函数内部的this原本指向的是obj1，但是在(2)处，由于经过了new操作符调用，bar函数内部的this却重新指向了返回的实例，这就可以说明new 绑定的优先级是要高于显式绑定的。

至此，四种绑定规则的优先级排序就已经得出了,分别是

 > new 绑定 > 显式绑定 > 隐式绑定 > 默认绑定

### 箭头函数中的this绑定

箭头函数是ES6里一个重要的特性。

箭头函数的 `this` 是根据外层的（函数或者全局）作用域来决定的。函数体内的 `this` 对象指的是定义时所在的对象，而不是之前介绍的调用时绑定的对象。举一个例子

```js
var a = 1
var foo = () => {
    console.log(this.a) // 定义在全局对象中，因此this绑定在全局作用域
}

var obj = {
    a: 2
}
foo() // 1 ,在全局对象中调用
foo.call(obj) // 1,显示绑定，由obj对象来调用，但根本不影响结果
```

从上面这个例子看出，箭头函数的 `this` 强制性的绑定在了箭头函数定义时所在的作用域，而且无法通过显示绑定，如 `apply,call` 方法来修改。

再来看下面这个例子

```js
// 定义一个构造函数
function Person(name,age) {
    this.name = name
    this.age = age 
    this.speak = function (){
        console.log(this.name)
        // 普通函数（非箭头函数),this绑定在调用时的作用域
    }
    this.bornYear = () => {
        // 本文写于2020年，因此new Date().getFullYear()得到的是2020
        // 箭头函数，this绑定在实例内部
        console.log(new Date().getFullYear() - this.age)
    }
}

var ahui = new Person("ahui",28)

ahui.speak() // "ahui"
ahui.bornYear() // 1992
// 到这里应该大家应该都没什么问题

var angeli = {
    name: "angeli",
    age: 18  // 永远18岁
}

ahui.speak.call(angeli)
// "angeli" this绑定的是angeli这个对象

ahui.bornYear.call(angeli)
// 1992 而不是 2002,这是因为this永远绑定的是ahui这个实例

```
因此 ES6 的箭头函数并不会使用四条标准的绑定规则，而是根据当前的词法作用域来决定 `this` ，具体来说就是，箭头函数会继承 外层函数调用的 `this` 绑定 ，而无论外层函数的 `this` 绑定到哪里。

小结
以上就是javascript中所有this绑定的情况，在es6之前，前面所说的四种绑定规则可以涵盖任何的函数调用情况，es6标准实施以后，对于函数的扩展新增了箭头函数，与之前不同的是，箭头函数的作用域位于箭头函数定义时所在的作用域。

而对于之前的四种绑定规则来说，掌握每种规则的调用条件就能很好的理解this到底是绑定在了哪个作用域。

原文地址： [JavaScript中this绑定详解](https://segmentfault.com/a/1190000007101339)