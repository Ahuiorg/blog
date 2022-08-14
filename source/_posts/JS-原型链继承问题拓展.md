---
uuid: 49c30ae5-120c-11ed-aacc-b11deac0aae6
title: JS 原型链继承问题拓展
abbrlink: 3012898819
date: 2020-03-13 22:35:34
tags:
---
**原型链继承存在的问题**：

1. 包含引用类型的原型属性会被所有实例共享，这会导致对一个实例的修改会影响到另一个实例。在通过原型来实现继承时，原型实际上会变成另一个类型的实例。原先的实例属性就变成了现在的原型属性

2. 在创建了类型的实例时，不能向超类型的构造函数中传递参数

<!-- more -->

```js
function SuperType () {
  this.citys = ['北京', '上海', '深圳'];
}

function SubType () {
  this.name = 'sub';
}

SubType.prototype = new SuperType();

var instance1 = new SubType();

instance1.citys.push('阿斯加德'); 

console.log(instance1.citys) // ["北京", "上海", "深圳", "阿斯加德"]

var instance2 = new SubType();

console.log(instance2.citys) // ["北京", "上海", "深圳", "阿斯加德"]
```

上面的代码完美的解释了原型链继承的时候**包含引用类型的原型属性会被所有实例共享，这会导致对一个实例的修改会影响到另一个实例**


把代码稍微改一下
```js
function SuperType () {
  this.citys = ['北京', '上海', '深圳'];
}

function SubType () {
  this.name = 'sub';
}

SubType.prototype = new SuperType();

var instance1 = new SubType();

instance1.citys = ['阿斯加德'];

console.log(instance1.citys) // ["阿斯加德"]

var instance2 = new SubType();

console.log(instance2.citys) // ["北京", "上海", "深圳"]
```

这里会的发现 `instance1.citys` 是 ["阿斯加德"]， 这个完全没毛病，但是 `instance2.citys`为啥是 ["北京", "上海", "深圳"] 呢？ 而不是 ["阿斯加德"] ？

难道是我们上边说的 **包含引用类型的原型属性会被所有实例共享，这会导致对一个实例的修改会影响到另一个实例**  有问题 ？

如果你也像我这样， 对这个不清楚， 那么下边跟我一起分析一下吧。

不管怎样， 碰到这种问题， 我们先去看一下 instance1 跟 instance2 到底是个啥。。。。
```js
function SuperType () {
  this.citys = ['北京', '上海', '深圳'];
}

function SubType () {
  this.name = 'sub';
}

SubType.prototype = new SuperType();

var instance1 = new SubType();

instance1.citys = ['阿斯加德'];

console.log(instance1)

var instance2 = new SubType();

console.log(instance2)
```
于是我们就会看到如下结果
```js
console.log(instance1); // SubType {name: "sub", citys: Array(1)}
console.log(instance2); // SubType {name: "sub"}
```
很明显的发现，只有`instance1`才有 `citys` 这个属性， 而 `instance2` 是没有 `citys` 这个属性的。这是为啥呢？ 

在原型链里有一句我们经常说到的话：**当自身没有这个属性的时候，就会去它的原型链上找**。 这里很明显，我们给 `instance1` 添加了一个 `citys` 的属性，所以能够找到 `citys` ， 而 `instance2` 的话， 自身没有 `citys` 但是在它的原型链上， `SuperType` 是有 `citys` 属性的， 所以也能访问得到。也可以通过下边的结果看出来
```js
console.log(instance1.hasOwnProperty('citys')) // true   说明这个citys属性是自身的属性，而不是继承而来的
console.log(instance2.hasOwnProperty('citys')) // false  说明这个citys属性是通过继承得到的
```

通过上边的分析， 我们不难理解为何当 `instance1.citys = ['阿斯加德']` 的时候， `instance2.citys`输出的是 ["北京", "上海", "深圳"] 了

原因就是 `instace1` 上的 `citys` 是赋值操作的时候给这个实例新增的一个属性，而 `instance2` 上的 `citys` 属性是从 `SuperType` 上继承过来的，所以对 `instance1` 进行赋值时，对 `instance2` 没有影响。

这里不要跟 `instance1.citys.push(['阿斯加德'])` 这个操作混淆了，很明显， 这里 `instance1.citys.push()` 的操作， 是对 `instance1` 从 `SuperType` 里边继承过来的 `citys` 属性进行的操作，这个属性是共享的，在一个实例里修改过会影响其它实例，所以会对 `instance2.citys` 有影响。

如果还没有理解，那就再看一下下边代码，加深一下理解
```js
function SuperType () {
  this.citys = ['北京', '上海', '深圳'];
}

function SubType () {
  this.name = 'sub';
}

SubType.prototype = new SuperType();

var instance1 = new SubType();

instance1.citys = ['阿斯加德'];
console.log(instance1.citys) // ["阿斯加德"]

var instance2 = new SubType();
console.log(instance1.citys) // ["北京", "上海", "深圳"]

delete instance1.citys

console.log(instance2.citys) // ["北京", "上海", "深圳"]
```