---
title: JS prototype
abbrlink: prototype
date: 2020-02-29 01:59:58
tags: [js,web,浏览器]
categories: [前端基础]
---

在典型的面向对象的语言中，如java，都存在类（class）的概念，类就是对象的模板，对象就是类的实例。但是在Javascript语言体系中，是不存在类（Class）的概念的，javascript中不是基于‘类的'，而是通过构造函数（constructor）和原型链（prototype chains）实现的。

<!-- more -->

在ES6中提供了更接近传统语言的写法，引入了Class（类）这个概念，作为对象的模板。通过class关键字，可以定义类。基本上，ES6的class可以看作只是一个语法糖，它的绝大部分功能，ES5都可以做到，新的class写法只是让原型对象的写法更加清晰、更像面向对象编程的语法而已。

### 构造函数(constructor)的简单介绍

　　所谓构造函数，就是提供了一个生成对象的模板并描述对象的基本结构的函数。一个构造函数，可以生成多个对象，每个对象都有相同的结构。总的来说，构造函数就是对象的模板，对象就是构造函数的实例。
构造函数的特点有：
　　a：构造函数的函数名首字母必须大写。
　　b：内部使用this对象，来指向将要生成的对象实例。
　　c：使用new操作符来调用构造函数，并返回对象实例。

看一个最简单的一个例子。

```js
function Person(name) {
  this.name =  name;
}

var boy = new Person('Ahui')
console.log(boy.name); // Ahui
```

### 构造函数的缺点

　　所有的实例对象都可以继承构造函数中的属性和方法。但是，同一个对象实例之间，无法共享属性。

```js
function Person(name, height) {
  this.name = name;
  this.height = height;
  this.hobby = function() {
    return 'watching movies';
  }
}

var boy = new Person('keith',180);
var girl = new Person('rascal',168);
console.log(boy.name); //'keith'
console.log(girl.name); //'rascal'
console.log(boy.hobby === girl.hobby); //false
```
　　上面代码中，一个构造函数Person生成了两个对象实例boy和girl，并且有两个属性和一个方法。但是，它们的hobby方法是不一样的。也就是说，每当你使用new来调用构造函数放回一个对象实例的时候，都会创建一个hobby方法。这既没有必要，又浪费资源，因为所有hobby方法都是统一的行为，完全可以被两个对象实例共享。

　　所以，构造函数的缺点就是： **同一个构造函数的对象实例之间无法共享属性或方法**

### prototype属性的作用

　　为了解决构造函数的对象实例之间无法共享属性的缺点，js提供了prototype属性。

　　js中每个数据类型都是对象（除了null和undefined），而每个对象都继承自另外一个对象，后者称为“原型”（prototype）对象，只有null除外，它没有自己的原型对象。

　　**原型对象上的所有属性和方法，都会被对象实例所共享。**

　　通过构造函数生成对象实例时，会将对象实例的原型指向构造函数的prototype属性。每一个构造函数都有一个prototype属性，这个属性就是对象实例的原型对象。

```js
function Person(name, height) {
  this.name = name;
  this.height = height;
}
Person.prototype.sayname = function() {
  return this.name;
}

var boy = new Person('Ahui', 183);
var girl = new Person('Ajiao', 168);

console.log(boy.sayname()); // Ahui
console.log(girl.sayname()); // Ajiao
console.log(boy.sayname === girl.sayname); // true

```
　　上面代码中，如果将hobby方法放在原型对象上，那么两个实例对象都共享着同一个方法。我希望大家都能理解的是，对于构造函数来说，prototype是作为构造函数的属性；对于对象实例来说，prototype是对象实例的原型对象。所以prototype即是属性，又是对象。

　　原型对象的属性不是对象实例的属性。对象实例的属性是继承自构造函数定义的属性，因为构造函数内部有一个this关键字来指向将要生成的对象实例。对象实例的属性，其实就是构造函数内部定义的属性。只要修改原型对象上的属性和方法，变动就会立刻体现在所有对象实例上。

　　上面代码中，当修改了原型对象的hobby方法之后，两个对象实例都发生了变化。这是因为对象实例其实是没有hobby方法，都是读取原型对象的hobby方法。也就是说，当某个对象实例没有该属性和方法时，就会到原型对象上去查找。如果实例对象自身有某个属性或方法，就不会去原型对象上查找。

```js
function Person(name, height) {
  this.name = name;
  this.height = height;
}
Person.prototype.hobby = function() {
  return 'swimming';
}
var boy = new Person('Ahui', 183);
var girl = new Person('Ajiao', 168);
boy.hobby = function(){
  return 'play basketball';
}
console.log(boy.hobby()); //'play basketball'
console.log(girl.hobby()); //'swimming'
console.log(boy.hobby === girl.hobby); // false

```

　　上面代码中，boy对象实例的hobby方法修改时，就不会在继承原型对象上的hobby方法了。不过girl仍然会继承原型对象的方法。这个时候boy.hobby与girl.hobby就不相等了

　　总结一下：
　　a：原型对象的作用，就是定义所有对象实例所共享的属性和方法。
　　b：prototype，对于构造函数来说，它是一个属性；对于对象实例来说，它是一个原型对象。

### 原型链（prototype chains）

　　对象的属性和方法，有可能是定义在自身，也有可能是定义在它的原型对象。由于原型对象本身对于对象实例来说也是对象，它也有自己的原型，所以形成了一条原型链（prototype chain）。比如，a对象是b对象的原型，b对象是c对象的原型，以此类推。所有一切的对象的原型顶端，都是Object.prototype，即Object构造函数的prototype属性指向的那个对象。

　　当然，Object.prototype对象也有自己的原型对象，那就是没有任何属性和方法的null对象，而null对象没有自己的原型。

```js
console.log(Object.getPrototypeOf(Object.prototype)); //null

console.log(Person.prototype.isPrototypeOf(boy)) //true
```

　　原型链（prototype chain）的特点有：
　　　　a：读取对象的某个属性时，JavaScript引擎先寻找对象本身的属性，如果找不到，就到它的原型去找，如果还是找不到，就到原型的原型去找。如果直到最顶层的Object.prototype还是找不到，则返回undefined。
　　　　b：如果对象自身和它的原型，都定义了一个同名属性，那么优先读取对象自身的属性，这叫做“覆盖”（overiding）。
　　　　c：一级级向上在原型链寻找某个属性，对性能是有影响的。所寻找的属性在越上层的原型对象，对性能的影响越大。如果寻找某个不存在的属性，将会遍历整个原型链。

　　看概念可能比较晦涩，我们来看一个例子。但是理解了概念真的很重要。

```js
var arr = []
console.log(arr.length;  // 0
console.log(arr.join()); //
console.log(arr.valueOf()); // []
```
上面代码中，定了一个数组arr，数组里面有三个元素。我们并没有给数组添加任何属性和方法，可是却在调用length，join()，valueOf()时，却不会报错。

　　length属性是继承自Array.prototype的，属于原型对象上的一个属性。join方法也是继承自Array.prototype的，属于原型对象上的一个方法。这两个方法是所有数组所共享的。当实例对象上没有这个length属性时，就会去原型对象查找。

　　valueOf方法是继承自Object.prototype的。首先，arr数组是没有valueOf方法的，所以就到原型对象Array.prototype查找。然后，发现Array.prototype对象上没有valueOf方法。最后，再到它的原型对象Object.prototype查找。

　　来看看Array.prototype对象和Object.prototype对象分别有什么属性和方法。

```js
console.log(Object.getOwnPropertyNames(Array.prototype))
//["length", "toSource", "toString", "toLocaleString", "join", "reverse", "sort", "push", "pop", "shift", "unshift", "splice", "concat", "slice", "lastIndexOf", "indexOf", "forEach", "map", "filter", "reduce", "reduceRight", "some", "every", "find", "findIndex", "copyWithin", "fill", "entries", "keys", "values", "includes", "constructor", "$set", "$remove"]
 console.log(Object.getOwnPropertyNames(Object.prototype))
 // ["toSource", "toString", "toLocaleString", "valueOf", "watch", "unwatch", "hasOwnProperty", "isPrototypeOf", "propertyIsEnumerable", "__defineGetter__", "__defineSetter__", "__lookupGetter__", "__lookupSetter__", "__proto__", "constructor"]
```
　　我相信，大家看到这里，对prototype还是似懂非懂的。这很正常，毕竟是js中比较重要又比较抽象的概念,不可能那么快就掌握，再啃多几篇，说不定掌握其精髓。在某乎上，有一个活生生的实例，可能也是大家会遇到的问题。可以看看 js构造函数和原型对象。

### constructor属性

　　prototype对象有一个constructor属性，默认指向prototype对象所在的构造函数。

　　要注意的是，prototype是构造函数的属性，而constructor则是构造函数的prototype属性所指向的那个对象，也就是原型对象的属性。注意不要混淆。

　　由于constructor属性是定义在原型（prototype）对象上面，意味着可以被所有实例对象继承。

```js
function A() {};
var a = new A();
console.log(a.constructor); //A()
console.log(a.constructor === A.prototype.constructor);//true
```
上面代码中，a是构造函数A的实例对象，但是a自身没有contructor属性，该属性其实是读取原型链上面的A.prototype.constructor属性。

#### constructor属性的作用

　　　a：分辨原型对象到底属于哪个构造函数

```js
function A(){};
var a = new A();
console.log(a.constructor === A) //true
console.log(a.constructor === Array) //false
```
上面代码表示，使用constructor属性，确定实例对象a的构造函数是A，而不是Array。

　　　　b：从实例新建另一个实例

```js
function A() {};
 var a = new A();
var b = new a.constructor();
console.log(b instanceof A); //true
```
上面代码中，a是构造函数A的实例对象，可以从a.constructor间接调用构造函数。

　　　　c：调用自身的构造函数成为可能
```js
A.prototype.hello = function() {
return new this.constructor();
}
```
　　　　d：提供了一种从构造函数继承另外一种构造函数的模式

```js
function Father() {}
function Son() {
  Son.height.constructor.call(this);
}
Son.height = new Father();
 ```
上面代码中，Father和Son都是构造函数，在Son内部的this上调用Father，就会形成Son继承Father的效果。

　　　　e：由于constructor属性是一种原型对象和构造函数的关系，所以在修改原型对象的时候，一定要注意constructor的指向问题。

　　　　解决方法有两种，要么将constructor属性指向原来的构造函数，要么只在原型对象上添加属性和方法，避免instanceof失真。

### instanceof运算符

　　instanceof运算符返回一个布尔值，表示指定对象是否为某个构造函数的实例。

　　因为instanceof对整个原型链上的对象都有效，所以同一个实例对象，可能会对多个构造函数都返回true。

　注意，instanceof对象只能用于复杂数据类型（数组，对象等），不能用于简单数据类型（布尔值，数字，字符串等）。

　　此外，null和undefined都不是对象，所以instanceof 总是返回false。

　　利用instanceof运算符，还可以巧妙地解决，调用构造函数时，忘了加new命令的问题。

　　上面代码中，使用了instanceof运算符来判断函数体内的this关键字是否指向构造函数Keith的实例，如果不是，就表明忘记加new命令，此时构造函数会返回一个对象实例，避免出现意想不到的结果。

[原文出处>>>](https://www.cnblogs.com/douyage/p/8630529.html)