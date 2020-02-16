---
title: es6 小记
abbrlink: 773176463
date: 2020-02-15 14:25:25
tags:
---

### 6. 结构赋值

#### object 结构赋值

```js
const person = {
  name: "ahui",
  age: "18",
  city: "BJ",
  social: {
    www: "didiorg.com";
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
let b = ((2)[(a, b)] = [b, a]);
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
