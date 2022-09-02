---
title: JS new 一个对象的过程
abbrlink: 3315289936
date: 2020-02-29 19:36:50
tags: [js,web,浏览器]
categories: [前端基础]
---

首先让我回忆一下创建对象的三种方法
<!-- more -->

1. 单休模式
```js
const Person  = {
  name: "angelee",
  sayname: function() {
    return this.name;
  }
}

let ahui = Object.create(Person);
ahui.name = "ahui"
console.log(ahui.sayname()) // ahui

```

2. 构造函数
```js
const Person = function (name) {
  this.name = name;
}
Person.prototype.sayname = function () {
  return this.name;
}

let ahui = new Person("ahui");
console.log(ahui.sayname()); // ahui
```

3. ES6 class
```js
class Person {
  constructor (name) {
    this.name = name;
  }
  sayname () {
    return this.name;
  }
}

let ahui = new Person('ahui');
console.log(ahui.sayname()); // ahui

// 这里再回忆一下 class 的继承

class Boy extends Person {
  constructor (name, age) {
    super(name)
    this.age = age
  }
  sayage() {
    return this.age
  }
}

let angelee = new Boy('angelee', 18);
console.log(angelee.sayname()); // angelee
console.log(angelee.sayage()); // 18
```

我们发现， 创建对象的时候，除了单例是通过 `Object.create()`创建对象，其它都是通过 `new` 操作实现的。

那`new` 到底做了什么呢 ？ 

让我们以下边这个为例详细分析一下

```js
const Person = function (name) {
  this.name = name;
}
const ahui = new Person('ahui')
console.log(ahui.name); // ahui
```

让我们试一下其它方法实现 `console.log(ahui.name)`也能正常输出 `ahui`


1. 先创建一个空对象
```js
const ahui = new Object()
```

2. 设置原型链，把 ahui 的`__proto__`成员指向 Person 函数对象的`prototype`成员对象
```js
ahui.__proto__ = Person.prototype
```

3. 把Person中的`this`指向ahui
```js
const result = Person.call(ahui)
```

4. 将初始化完毕的新对象地址，保存到等号左边的变量中。判断Person的返回值类型，如果是值类型，返回obj。如果是引用类型，就返回这个引用类型的对象


### 自己实现一个 new 方法

第一种方法
```js
const Person = function (name) {
  this.name = name;
}

// 自己实现一个 new 方法
function myNew() {

  // 1. 新建一个空对象
  var obj = new Object();


  // shift() 方法从数组中删除第一个元素，并返回该元素的值
  var Constructor = [].shift.call(arguments); 

  // 2. 建立继承关系(二者之间的关系)也就是把新对象的__proto__指向构造函数的 prototype
  obj.__proto__ = Constructor.prototype;

  // 3. 改变 this 指向，并开始执行这个构造函数的内容
  var ret = Constructor.apply(obj, arguments);

  // 4. 返回创建的对象： 看一下构造函数的返回值，是对象还是一个基本数据类型?
  return typeof ret === 'object' ? ret : obj;
}

const ahui = new Person('ahui');
console.log(ahui.name); // ahui

const angeli = myNew(Person, 'angeli')
console.log(angeli); // angeli

angeli.name = 'angeli new'
console.log(angeli.name); // angeli new

```

第二种方法
```js
// 通过分析原生的new方法可以看出，在new一个函数的时候，
// 会返回一个func同时在这个func里面会返回一个对象Object，
// 这个对象包含父类func的属性以及隐藏的__proto__
function New(f) {
  //返回一个func
  return function () {
    console.log(arguments)
    
    // 新建一个对象， 并且把新对象的 __proto__ 指向构造函数的 prototype 属性
    var o = {"__proto__": f.prototype};

    // 改变 this 指向, 并执行原来构造函数里边的内容
    f.apply(o, arguments); // 继承父类的属性

    return o; //返回一个Object
  }
}

const Person = function (name) {
  this.name = name;
}

const ahui = New(Person)('ahui')
console.log(ahui.name) // ahui
```

自测一下
```js

function New(f) {
  return function () {
    
    let o = { "__proto__": f.prototype }

    f.apply(o, arguments)

    return typeof o === "object" ? o : f
  }
}

const Person = function (name) {
  this.name = name
}

Person.prototype.sayname = function() {
  return this.name
}

const ahui = New(Person)('ahui')
console.log(ahui.name) // ahui
ahui.sayname() // ahui
```

关于 `arguments` 可以参考这篇文章： [JS 函数实参转换为数组](/1882318475.html)