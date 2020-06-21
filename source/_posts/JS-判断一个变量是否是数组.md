---
abbrlink: 1
---
---
title: "JS判断一个变量是否是数组"
abbrlink: isArray
date: 2020-01-30 12:23:45
tags: [js,web,浏览器,array]
categories: [前端基础]
--- 

### 1. isArray()

[isArray](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray) 是 Array 的一个方法， 如果是数组返回 true, 否则返回 false

<!-- more -->

```js
var a = [1, 2, 3];
console.log(typeof a); //返回“object”
console.log(Array.isArray(a)); //true
```

### 2. instanceof

[instanceof 运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof)用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上。

```js
object instanceof constructor
object // 实例
constructor // 构造函数
```
用来检测 `constructor.prototype` 是否存在于参数 object 的原型链上 (`object.__proto__/object.__proto__.__proto__.......__proto__`)

```js
var arr = new Array();
var arr = ["a", "b", "c"];
var obj = { a: "a", b: "b", c: "c" };
console.log(arr instanceof Array); //true
console.log(arr instanceof Object); //true
console.log(obj instanceof Array); //false
console.log(obj instanceof Object); //true
```

> arr 的原型链上存在 Array.prototype 和 Object.prototype
> 只有 Array 类型的变量才会满足 arr instanceof Array 和 arr instanceof Object 都返回 true
> 也只有 Object 类型变量才满足 obj instanceof Array 返回 false，obj instanceof Object 返回 true

### 3. constructor

[constructor](https://www.w3school.com.cn/jsref/jsref_constructor_array.asp) 是 [Array 对象](https://www.w3school.com.cn/jsref/jsref_obj_array.asp)的一个属性，该属性返回对创建此对象的数组函数的引用

```js
var arr = ["a", "b", "c"];
var obj = { a: "a", b: "b", c: "c" };
console.log(arr.constructor === Array); //true
console.log(arr.constructor === Object); //false
console.log(obj.constructor === Object); //true
```

### 4. Object.prototype.toString.call()

[toString()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)表示返回该对象的字符串

> 每个对象都有一个 toString() 方法，当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。默认情况下，toString() 方法被每个 Object 对象继承。如果此方法在自定义对象中未被覆盖，toString() 返回 "[object type]"，其中 type 是对象的类型。

[call()](https://www.w3school.com.cn/js/js_function_call.asp)用来改变 this 指向，能够使用属于另一个对象的方法。

> 比如对象 b 没有方法 f(), 但是对象 a 有， 就可以直接 a.f.call(b), 这样 b 就使用了 a 里边的一个方法

更多关于 call() 的用法可以看[这里>>>](https://segmentfault.com/a/1190000018270750)

```js
var a = NaN;
var b = "222";
var c = null;
var d = false;
var e = undefined;
var f = Symbol();
var arr = ["aa", "bb", "cc"];
var obj = { a: "aa", b: "bb", c: "cc" };
var res = Object.prototype.toString.call(arr);
console.log(res); //[object Array]
var res2 = Object.prototype.toString.call(obj);
console.log(res2); //[object Object]
var res3 = Object.prototype.toString.call(a);
console.log(res3); //[object Number]
var res4 = Object.prototype.toString.call(b);
console.log(res4); //[object String]
var res4 = Object.prototype.toString.call(c);
console.log(res4); //[object Null]
var res5 = Object.prototype.toString.call(d);
console.log(res5); //[object Boolean]
var res6 = Object.prototype.toString.call(e);
console.log(res6); //[object Undefined]
var res7 = Object.prototype.toString.call(f);
console.log(res7); //[object Symbol]
```

我们可以使用`Object.prototype.toString.call(arr) === '[object Array]'`来判断变量是否是数组
