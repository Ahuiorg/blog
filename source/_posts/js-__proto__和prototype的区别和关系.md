---
title: js __proto__和prototype的区别和关系
abbrlink: proto
date: 2020-02-15 12:23:39
tags: [js, 浏览器]
categories: 面试
---

### 首先，看别人怎么说的：

1.在JS里，万物皆对象。方法（Function）是对象，方法的原型(Function.prototype)也是对象。
它们都会具有对象共有的特点:
对象具有属性 `__proto__`，可称为隐式原型，**一个对象的隐式原型指向构造该对象的构造函数的原型**，这也保证了实例能够访问在构造函数原型中定义的属性和方法。
  
2.方法(Function)
方法这个特殊的对象，除了和其他对象一样有上述 `__proto__` 属性之外，还有自己特有的属性——原型属性（prototype），这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例**共享**的属性和方法（我们把这个对象叫做原型对象）。
原型对象也有一个属性，叫做constructor，这个属性包含了一个指针，指回原构造函数。

<img src="/images/web/js/proto.jpg" align="center" style="margin: 0 auto;">

1.构造函数Foo()构造函数的原型属性Foo.prototype指向了原型对象，在原型对象里有共有的方法，所有构造函数声明的实例（这里是f1，f2）都可以共享这个方法。

2.原型对象Foo.prototypeFoo.prototype保存着实例共享的方法，有一个指针constructor指回构造函数。

3.实例f1和f2是Foo这个对象的两个实例，这两个对象也有属性__proto__，指向构造函数的原型对象，这样子就可以像上面1所说的访问原型对象的所有方法啦。另外：构造函数Foo()除了是方法，也是对象啊，它也有__proto__属性，指向谁呢？指向它的构造函数的原型对象呗。函数的构造函数不就是Function嘛，因此这里的__proto__指向了Function.prototype。其实除了Foo()，Function(), Object()也是一样的道理。


### 定义

`__proto__`（隐式原型)
　　JavaScript中任意对象都有一个内置属性[[prototype]]，在ES5之前没有标准的方法访问这个内置属性，但是大多数浏览器都支持通过__proto__来访问

[`prototype`（显式原型）](/prototype.html)
　　每一个函数在创建之后都会拥有一个名为prototype的属性，这个属性指向函数的原型对象。


js中每个数据类型都是对象（除了null和undefined），而每个对象都继承自另外一个对象，后者称为“原型”（prototype）对象，只有null除外，它没有自己的原型对象。

> 每一个函数在创建之后都会拥有一个名为prototype的属性，这个属性指向函数的原型对象。

### `__proto__` 跟 prototype的关系

1. 对象有属性`__proto__`, 指向该对象的构造函数的原型对象。

2. 方法除了有属性`__proto__`,还有属性`prototype`，`prototype`指向该方法的原型对象。

## 然后，自己理解


```js
// 我有一个构造函数 Person
function Person (name) {
  this.naem = name
}
// 这个构造函数的原型对象是 Person.prototype

// 通过Pserson 实例出一个人 ahui
let ahui = new Person('ahui');
// 现在 ahui 也是一个对象

// 则 ahui 的 __proro__ 指向 Person 的原型对象 Person.prototype
console.log(ahui.__proto__ === Person.prototype) // true
```
通过上边的代码， 我们是不是可以想像一下: 
构造函数`funcion Person ` 是不是也可以理解它是另外一个构造函数构`Funcion`造出来的 ?
```js
function Person() {}
console.log(Person.__proto__ === Function.prototype) // ture
```
想像成立！！

那这个时间是不是又有可能在想 `Function` 又是谁造出来的呢？ 
```js
console.log(Function.__proto__ === Object.prototype) // false
console.log(Function.prototype === Object.__proto__) // true
```
这。。。。

原来这里又进入了一个新世界 。。。。

参考下面文档：

[JavaScript instanceof 运算符深入剖析](https://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/)

[js中__proto__和prototype的区别和关系](https://www.zhihu.com/question/34183746)

[JS中Function和Object的关系研究分析](https://blog.csdn.net/liu_yunzhao/article/details/90085497)

[JavaScript之Function 和 Object 的区别和联系](https://www.cnblogs.com/wjw-blog/p/7002202.html)

[JS Function与Object关系](https://www.jianshu.com/p/5f57dd643bfd)