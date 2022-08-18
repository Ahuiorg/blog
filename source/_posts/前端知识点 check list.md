---
title: 前端知识点 check list
abbrlink: web
date: 2020-01-14 18:32:15
top: true
tags: [web]
categories: [前端基础]
---

## Don't be anxious, just do it.
<!--  -->
<!-- more -->

### JS

1. [x] [浏览器缓存](/cache.html)
2. [x] [性能优化 地址栏输完 url， 回车之后，浏览器做了哪些工作](/3152193188.html)
3. [x] [JS数据类型 基本数据类型和复杂类型的区别](/1405041715.html)
4. [x] [js prototype的理解](/prototype.html)
5. [x] [js 中`__proto__`和 `prototype` 的区别和关系](/proto.html)
6. [x] [判断变量是数组的方法](/isArray.html)
7. [x] [原型与原型链](/1015335320.html)
8. [x] [new 一个对象的过程](/3315289936.html)
9. [x] [JS 函数实参转换为数组](/1882318475.html)
10. [x] [typeof instanceof 底层原理实现](/3369490582.html)
11. [x] [this指向,箭头函数特点](/3915840815.html)
<!-- 12. [ ] call apply bind 的区别 bind方法的实现 -->
12. 事件轮询, promise, async await
5. [ ] Promisetry产生的初衷 可以用catch吗 [什么是Promise.try](https://segmentfault.com/a/1190000018586947)
13. script 标签 defer async
14. axois实现原理
15. 对象、数组、map、set，等遍历的方法，分别怎么用的，什么原理（想要for of能遍历原型上的东西，可以用iterator）
16. 跨域以及使用cors浏览器需要做什么处理  为什么要跨域，为什么要有事件轮询的机制  限制了什么，主要是限制cookie
17. 各种继承方案（主要是红宝书里的）
18. 三次握手，四次挥手详细了解，对https的了解
19. cookie和session区别（cookie的数据是服务端返回后通过什么存到浏览器的，然后跨域会到服务器么，（分两个阶段）， cookie知多少包括用法，特性，domain和基本封装）
20. 防止js攻击，xss crfs知多少
21. 预编译以及变量声明提升函数提升等
22. Websocket
23. 对浏览器兼容性的理解和方法或者做过哪些兼容性处理
24. 正则表达式匹配（转换时间，验证手机号等）
3. [ ] symbol作用
9. [ ] es5和es6区别
10. [ ] es5实现es6的class

### JS 算法

1. [ ] 数据去重
2. [ ] 去掉首尾空格
3. [ ] 排序
4. [ ] 深度拷贝
5. [ ] 斐波那契数列求和
6. [ ] js操tabletable作并对排序
7. [ ] 找出所有乘数

### CSS

1. [ ] flex(需要详细说明每个属性),flex1是什么
2. [ ] positionsticky
3. [ ] 盒模型
4. [ ] 百分比和vh的区别，height 100%和100vh的区别
5. [ ] em,rem计算
6. [ ] 垂直居中方式
7. [ ] BFC相关知识
8. [ ] 重排触发重新绘制的原理
9. [ ] 节流防抖实现

### React

1. [ ] 做过和哪些回流和重绘相关的优化
2. [ ] 虚拟dom的diff
3. [ ] useEffect
4. [ ] hook
5. [ ] 新的声明周期
6. [ ] immutablereselect和有什么好处
7. [ ] 生命周期hook
8. [ ] react列表key


### Vue
1. [ ] vue双向绑定的原理（vue答双向绑定的时候结合一些complie和watcher等流程答）（complie编译器编译的都是哪些东西，怎么抄编译的，把什么编译成什么）
2. [ ] 实现一个vue底层的简单监听函数，就是实现订阅者发布者模式
3. [ ] vue父子组件传值（多种方式）以及vuex
4. [ ] vue生命周期及每阶段都能做什么（vue父子组件嵌套生命周期的顺序）
5. [ ] vue router跳转的原理（刷新不刷新）
6. [ ] vue的高级用法
7. [ ] vuex是否是持久化的，持久化是需要结合本地缓存么
8. [ ] seo渲染是在服务器端的，会出现白屏情况，vue渲染是会先加载结构的，慢慢渲染
9. [ ] vue兼容性（2.0版本）（ie9以下不兼容因为object.defineProperty的兼容性）
10. [ ] vue源码知多少
11. [ ] jquery和vue区别和react的区别，mvc和mvvm框架区别，模块化那些区别
12. [ ] props穿的数据和data里数据的区别
13. [ ] Vue首次渲染页面时触发了哪些生命周期，props传数据时又触发了哪些生命周期
14. [ ] vuex的api可以set（也能实现数据劫持），v-html，v-for, v-if同时用会怎么样，v-once，插槽等



### Webpack

1. [ ] 做过哪些优化
2. 手写一个尽量多功能的webpack（快手问的QAQ）

### h5
1. [ ] 项目遇到的问题及解决方式

### Ts
1. [ ] 为什么要是用Ts

### 开放题
1. [ ] 如何设计组件table或UI

### 其它
1. [ ] IndexDB
2. [ ] 数据库选型
3. [ ] link用法原理能干啥与@import区别等
4. [ ] 浏览器几个标签页之间怎么传递数据
5. [ ] Flutter
6. [nginx 常用配置](/1508208186.html)

### 

#### js 异步编程
1. 回调
2. 事件监听
4. 发布/订阅
3. promise
5. generator yield;
6. async wait (终级方法)


#### promise 使用以及实现

击鼓传花， 每隔n出列一个人，求最后那个
mobox原理
node使用场景

柯理化实现
两个数组求交集

合并两个有序数组为一个有序数组
无序数组中的最大数和第二大数

Vue如何实现双向绑定
template模版编译过程
vue router 实现原理
navigator 实现原理

webpack 源码分析
https原理和tcp协议原理
http缓存问题

vuex的设计模式

深拷贝
数组去重
如何快速创建一个长度为100数值为1的数组
node next
盒子模型
垂直居中
flex布局跟浮动布局
排序算法
判断对象和数组
