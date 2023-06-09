---
title: 学习笔记1 ES6
abbrlink: 773176463
date: 2020-02-15 14:25:25
tags: [js, ES6, web]
categories: [learning notes]
---

课程地址: [【JS 老毕】Javascript ES6 基础+核心课程](https://www.bilibili.com/video/av67331423)

<!-- more  -->

### 9. 字符串方法和 for of

includes 是否包含，返回 true/false

```js
const string = "abc";
const substring = "ab";
console.log(string.includes(substring)); // true
```

startsWith 是否是以某个字符串开始， 返回 true/false
endsWith 是否是以某个字符串结尾， 返回 true/false

```js
const string = "abc";
const substring = "ab";
console.log(string.startsWith(substring)); // true
```

for of

```js
const tests = ["a", "b", "c", "d", "e"];
for (const test of tests) {
  console.log(test);
}

let maps = new Map();
maps.set("name", "ahui");
maps.set("phone", "17600888888");
maps.set("city", ["北京", "上海"]);

for (const [key, value] of maps) {
  console.log(key, value);
}
// name, ahui
// phone, 17600888888
// city, ["北京", "上海"]

const tests = ["a", "b", "c", "d", "e"];
// entries() 方法返回一个数组的迭代对象，该对象包含数组的键值对 (key/value)。
for (const test of tests.entries()) {
  console.log(test);
}
// (2) [0, "a"]
// (2) [1, "b"]
// (2) [2, "c"]
// (2) [3, "d"]
// (2) [4, "e"]
```

### 8. 函数参数默认值

```js
function orderCombo(comboName = "鸡块", drink = "可乐") {
  console.log(comboName);
  console.log(drink);
}
orderCombo("蛋炒饭");
```

### 7. 剩余参数和扩展参数 ...

剩余参数是把多个打包成一个数组
将一个数据拆分成多个项

```js
// 剩余参数
const tests = ["a", "b", "c", "d", "e"];
const [a, b, ...c] = tests;
// 会把数组 tests 里边后边三项组合成一个数组赋值给 c
console.log(a, b, c); // a, b, [c, d, e]

// 扩展参数
const a = "a";
const b = "b";
const c = ["a", "b", "c"];
console.log([a, b, ...c]); // [a, b , a, b, c]
```

### 6. 结构赋值

#### object 结构赋值

```js
const person = {
  name: "ahui",
  age: "18",
  city: "BJ",
  social: {
    www: "leejs.cn";
    email: "ahuinet@163.com"
  }
}

// 用已有的属性名 赋值
const {name, age, city} = person
const {www, email} = person.social
const {name, social: {www}} = person
console.log(name, age, city, www, email)

// 用新的变量名
const { name: ahuiName } = person  // ahui

// 添加默认值
const { mony = 1008610010 } = person
```

#### 数组的结构赋值

```js
const info = "ahui, 18, 1000000000";
const person = infor.split(",");
const [name, age, mony] = person;
console.log(name, age, mony); // ahui, 19, 1000000000

let a = 1;
let b = 2;
[a, b] = [b, a];
console.log(a, b); // 2, 1
```

### 5. map 对象

map set 数据的时候，key 是唯一的，如果 set 一个原来有的属性，就会更新原来对应 key 的值

```js
let maps = new Map();
// 添加
maps.set("name", "ahui");
maps.set("phone", "17600888888");
// 删除
maps.delete("name");
// 查找
maps.has("name");
// 获取
maps.get("name");
// 遍历
// for...of
```

### 4. set 对象

set 里边不会有重复的值

```js
let sets = new Set();
// 添加
sets.add(1); // 1 添加
sets.add(2); // 1, 2
// 删除
sets.delete(1); // 返回 true， 删除成功返回 ture， 删除失败返回 false
// 查找
sets.has(1); // 返回 false，查找
// 遍历
sets.forEach((item, i) => {});
```

### 3. 模板字符串 ``

```js
const ahuiMsg = {
  name: "ahui",
  age: "18",
  phone: "17600888888"
  email: "ahuinet@163.com"
}
const intro = `大家好我叫${ahuiMsg.name},今年${ahuiMsg.age}`
```

### 2. 箭头函数 =>

1. 能使用函数写法更简洁
2. 函数返回值可以被隐式返回，不需要写 return
3. 不重新绑定 this 的值

### 1. let const

let 定义变量
const 定义常量
var 可以重复定义变量，let const 不可以重复定义
var 函数作用域， let const 是块作用域

> 常用 const， 少用 let， 不用 var
