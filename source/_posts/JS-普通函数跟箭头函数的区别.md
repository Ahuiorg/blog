---
uuid: 49c30ae9-120c-11ed-aacc-b11deac0aae6
title: JS 普通函数跟箭头函数的区别
abbrlink: 3671102502
date: 2020-07-03 23:31:38
tags: [js, js 基础]
---

1. 箭头函数不会产生this, 会捕捉当前的执行上下文中的this当做自己的this

2. 箭头函数不能做为构造函数

3. 箭头函数不能绑定 arguments 

4. call/apply/bind 对箭头函数不起作用

5. 箭头函数没有原型属性

6. 箭头函数不能当作 Generator 函数，不能使用 yield 关键字
