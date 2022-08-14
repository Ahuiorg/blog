---
uuid: 49c30ae3-120c-11ed-aacc-b11deac0aae6
title: JS 函数实参转换为数组
abbrlink: 1882318475
date: 2020-02-29 20:49:38
tags: [js,web,浏览器,array]
categories: [前端基础]
---

实际参数在函数中我们可以使用 arguments 对象获得 （注：形参可通过 arguments.callee 获得），虽然 arguments 对象与数组形似，但仍不是真正意义上的数组。

<!-- more -->

### 0: [...arguments]

这个方法其实是用来代替下边方法一的

### 一：通过 `Array.prototype`属性调用 slice 方法

```js
const args = Array.prototype.slice.call(arguments)
```

Array 本身是没有 slice 方法，它的方法在 Array.prototype中，而我们在调用 slice 方法的时候，如果在 Array 本身没有找到 slice 方法的话，会通过它的原型链往上查找。


### 二. 通过调用[]的slice方法

```js
const args = [].sclice.call(arguments, 0)
```
注意这里是[]， 不是 Array,为什么呢？ 先看下边
```js
typeof [];  // object

typeof Array;   // Funcion

[].__proto__ === Array.prototype; // true
```

从上面很清楚的知识 `Array` 是一个构造函数，而 [] 是 Array 的实例， 等价于 `new Array()`,**因为实例的`__proto__` 指向该实例的构造函数的`prototype`**;

但是这里 `[] === new Array // false` 是 `false`， 因为对象是不能直接比较的




### 三. 通过遍历arguments,返回数组

```js
function toArray(){
  var args = []; 
  for (var i = 1; i < arguments.length; i++) { 
    args.push(arguments[i]); 
  } 
  return args;
}
```