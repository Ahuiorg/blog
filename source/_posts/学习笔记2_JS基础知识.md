---
title: 学习笔记2 JS基础知识
abbrlink: 1015335320
date: 2020-02-18 12:14:32
tags: [js]
categories: learning notes
---

课程地址: [JavaScript 基础知识](https://www.bilibili.com/video/av58604768)

<!-- more -->

### 基础数据类型

js 数据类型： 'usbno'

> undefined: 未定义
> string
> boolean
> number
> object
> null

JS 基本数据类型： `undefined, string, number, boolean, null`

> function 不是数据类型
> object 是复杂数据类型

#### NaN

not a number

> isNaN 跟谁都不相等， 包括它本身
> typeof NaN // "number"

检测一个变量是不是 NaN

> ES6: isNaN()
> ES5: a != a 为 false 不是 NaN， 为 true 则是 NaN

### js 作用域链

当前函数作用域找不到的时候就去它的父级找

```js
var a = 666;
function show() {
  var a = 233;
  show2();
}
function show2() {
  console.log(a);
}
show(); // 666
```

这是因为 show2 所在的位置是跟 show 一个作用域的，show 函数并不是 show2 函数的父级
在这里， show2 的父级跟 show 一样， 是 `Window`

```js
var a = 666;
function show() {
  var a = 2333;
  show2();
  function show2() {
    console.log(a);
  }
}
show(); // 2333
```

这里 show2 的父级就是 show， 所以会打印 2333

**当前函数作用域找不到的时候就去它的父级找** 这名话只跟物理位置有关，跟调用没关

### IIFE 匿名函数自执行

```js
(function() {
  console.log("我就是匿名函数自执行");
})();
```

1. 避免变量污染
2. 以前写框架的时候经常用

### 闭包

```js
function show() {
  var num = 666;
  return function() {
    console.log(num);
  };
}
const show2 = show();
show2(); // 666
```

```js
var arr = [];
for (var i = 0; i < 3; i++) {
  (function(i) {
    arr[i] = function() {
      return i;
    };
  })(i);
}
console.log(arr[0]()); // 0
console.log(arr[1]()); // 1
console.log(arr[2]()); // 2
```

### 事件流

> 捕获
> 冒泡

### this

在浏览器下，全局 this 指向 Window， 对象引用时 指向引用它的那个对象

1. 全局
   this 在浏览器下指向 Window

2. 函数 this

```js
function show() {
  console.log(this);
}
show(); // Window
```

```js
"use strict";
function show() {
  console.log(this);
}
show(); // undefined
```

3. 对象

```js
var info = {
  name: "ahui",
  showName: function() {
    console.log(this.name);
  }
};

info.showName(); // ahui
```

```js
// "use strict";  // undefined   如果是在严格模式下的时候， this 为 undefined
var name = "angelee";
var info = {
  name: "ahui",
  showName: function() {
    function showMyName() {
      console.log(this.name);
    }
    showMyName();
  }
};

info.showName(); // angelee
```

### call apply bind 区别

改变 this 指向

```js
function show() {
  console.log(this);
}
show(); // Window
```

```js
function show(a, b) {
  console.log(this, a, b);
}
show.call(666, 233, 6969); // Number {666} 233 6969
```

```js
var info = {
  name: "ahui",
  showName: function() {
    function showMyName() {
      console.log(this.name);
    }
    showMyName.call(this);
  }
};

info.showName(); // ahui
```

bind() 不会执行， 只传递 this 指向

> 当一个方法需要添加默认参数的时候用得多， 其它的情况一般用 call 或者 apply

```js
var info = {
  name: "ahui",
  showName: function() {
    showMyName = function() {
      console.log(this.name);
    }.bind(this);
    showMyName();
  }
};

info.showName(); // ahui
```

apply 跟 call 一样， 就是第二个参数是数组
而 call，如果碰到多个参数， 从第二个参数开始，就得一个一个写

```js
var arr = [12, 3, 5, 8];
Math.max.apply(null, arr); //12
```

### 面向对象编辑和简单的设计模式

#### 创建对象的三种方式

1. 单体模式

```js
var Ahui = {
  name: "angelee",
  age: 18,
  showName: function() {
    return this.name;
  }
};
Ahui.showName(); // "angelee"
```

2. 原型模式
   属性放在构造函数，方法放在原型上

```js
function Ahui(name, age) {
  this.name = name;
  this.age = age;
}
Ahui.prototype.showName = function() {
  return this.name;
};
var angelee = new Ahui("angelee", 18);
angelee.showName(); // "angelee"
```

3. 类模式
   通过 ea6 的 class 去定义对象

```js
class Ahui {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  showName() {
    return this.name + this.age;
  }
}
var angelee = new Ahui("angelee", 18);
angelee.showName(); // "angelee18"
```

### 面向对象继承

1. 单体模式下的继承
   通过 Object.create() 方法继承

```js
var Ahui = {
  name: "angelee",
  age: 18,
  showName: function() {
    return this.name;
  }
};

var DaHui = Object.create(Ahui);
DaHui.name = "lipenghui";
DaHui.age = 188;
DaHui.showAge = function() {
  return this.age;
};
console.log(DaHui.showName()); // liepnghui`
console.log(DaHui.showAge()); // 188
```

2. 通过 ea6 的 class 方法

```js
// 父类
class Ahui {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  showName() {
    return this.name;
  }
}

class DaHui extends Ahui {
  constructor(name, age, job) {
    super(name, age);
    this.job = job;
  }
  showInfo() {
    return `name:${super.showName()}, age: ${this.age}, job: ${this.job}`;
  }
}

var angelee = new DaHui("angelee", 16, "fe");
angelee.showInfo(); // "name:angelee, age: 16, job: fe"
```

记住关键字： **`extends`、 `super`** 就行， 这是规范， 刚开始觉得不习惯， 用久了之后， 就像用 if else 一样简单

### 跨域

1. JSONP 原理
1. js 是可以跨域的
1. 服务器返回的数据，是相当于一个函数调用
1. 本地 js 方法里边有一个方法的定义， 调用本地的方法的时候，就会去调用对应的函数
1. 只能是 get 方法， 如果要用 pust 就用 CROS

1. CROS
1. 必须需要服务器端配合开发，否则不行
   <!-- 2. access-allow -->
