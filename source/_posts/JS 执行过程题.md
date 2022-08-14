---
uuid: 49c2e3d2-120c-11ed-aacc-b11deac0aae6
title: JS 执行过程题
abbrlink: 1491244432
date: 2020-06-16 18:22:47
tags: [js, js算法]
---

执行过程，就是要理解js的执行上下文

<!-- more -->

### 构造函数

```js
function Person1(name) {
    this.name = name;
}

function Person2(name) {
    if (name) {
        this.name = name;
    }
}

function Person3(name) {
    this.name = name || 'jack';
    return {
        name: 'lilei',
    };
}

Person1.prototype.name = 'Tom';
Person2.prototype.name = 'Tom';
Person3.prototype.name = 'Tom';

console.log(new Person1().name, new Person2().name, new Person3().name);
```

> 第一个：当构造函数里边有 name 属性的时候， 就不会去原型上找了
> 第二个：当构造函数里边没有 name 属性的时候，就会去原型上找
> 第三个：当构造函数有返回值的时候，new 操作的时候就会返回 return 里边的内容

### 执行上下文，this

```js
var name = 'global';

function outer(name) {
    console.log(1, name); // outer12121
    console.log('arg1', arguments[0]); // outer12121

    var name = 'outer';

    console.log('arg2', arguments[0]); // outer
    console.log(2, name); // outer

    inner();
    console.log(3, this.name); //  global

    if (name) {
        console.log(4, name); // error
        let name = 'if-name';
    }
}
function inner() {
    console.log(5, name); // glboal
}

outer('outer12121');
```

> 这里注意一个 log(4)那里， 会报错， 因为在 if{}这个上下文中提前使用了 let 定义的 name，let 定义的变量是没有变量提升的
> 当函数里边有跟参数同名的变量时， 会改变原来参数的值，同时也会改变 arguments, 因为 arguments 其实就是一个引用

### this 指向的问题

```js
var name = 'global';
function sayName() {
    console.log(this.name);
}
var obj = {
    name: 'obj',
    sayName: function (fn) {
        fn && fn();
    },
};

obj.sayName(sayName); // global
```

```js
var a = 1;
var obj = {
    a: 2,
    getA: () => {
        return this.a;
    },
};
console.log(obj.getA()); // 1
```

> 箭头函数不会改变环境的 this， 所以这里的 this 不是 obj 对象里边的， 这个 obj.getA()就是一个方法，在全局下执行的一个方法，所以这里是 1

### JS 执行机制的问题

```js
new Promise((resolve, reject) => {
    console.log(1);
    setTimeout(() => {
        console.log(2);
    });
    resolve();
})
    .then(() => {
        console.log(3);
    })
    .then(() => {
        return new Promise((resolve, reject) => {
            console.log(4);
        }).then(() => {
            console.log(5);
        });
    })
    .then(() => {
        console.log(6);
    });
console.log(7);
// 1 7 3 4 2
```

> 这是一个很有意思的题目考了 js 事件轮循， js 执行机制，promise
> log(5) 跟 log(6) 的时候， 这里因为上 log(4)那个 promise 并没有完成(resolve 跟 reject 都没有被调用)， 所以 5， 6 是不会打印出来的
> js 执行的时候，是单线程的，通过事件轮循实现异步，每一次轮循都会把异步事件添加到队列中，这里的事件分为微任务(promise)跟宏任务(script, setTimeout, setInterval)，一个轮循结束之后，先执行微任务，再执行宏任务


```js
new Promise((resolve, reject) => {
  console.log(1)
  reject()
})
  .then(() => {
    console.log(2)
  }, () => {
    console.log(3)
  })
  .catch(() => {
    console.log(4);
    return Promise.reject()
  })
  .then(() => {
    console.log(5);
  })
  .catch(() => {
    console.log(6);
  })
```


### 异步任务分析

```js
console.log(1);
setTimeout(function () {
    console.log(2);
}, 1000);
setTimeout(function () {
    console.log(3);
}, 0);
console.log(4);
// 1 4 3 2
```
> 异步任务，当读取到异步任务的时候，将异步任务放置到Event table（事件表格）中，当满足某种条件或者说指定事情完成了（这里的是时间分别是达到了0ms和1000ms）当指定事件完成了才从Event table中注册到Event Queue（事件队列），当同步事件完成了，便从Event Queue中读取事件执。（因为3的事情先完成了，所以先从Event table中注册到Event Queue中，所以先执行的是3而不是在前面的2）

### `forEach` VS `for` & `for of`

```js
const list = [1, 2, 3]
const square = num => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(num * num)
    }, 1000)
  })
}

function test() {
  list.forEach(async x=> {
    const res = await square(x)
    console.log(res)
  })
}
test()

// 一秒后一次打印出 1， 4， 9
```
> `forEach` 是不能阻塞的，默认是请求并行发起

```js
const list = [1, 2, 3]
const square = (num) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(num * num)
    }, 1000)
  })
}

async function test() {
//   for (let i = 0; i < list.length; i++) {
//     const res = await square(list[i])
//     console.log(res)
//   }
  for(let i of list) {
    const res = await square(i)
    console.log("res: ", res);
  }
}
test()
// 一秒之后打印1， 再过一秒打印4， 再过一秒打印9
```
> `for` 跟 `for of` 是会阻塞的

### 普通函数跟箭头函数的区别

定义好的obj， 分别执行 obj.f1() obj.f2() new obj.f1() new obj.f2() 会发生什么

```js
const obj = {
  f1: function() {
    console.log(this);
  },
  f2: () => {
    console.log(this);
  }
}
obj.f1()
obj.f2()
new obj.f1()
new obj.f2()
```
**这是一个很能考察基本功的题目**

分析一下， 这里f1跟f2的区别就是，一个普通函数跟一个箭头函数的区别， 如果**真的很**了解[头函数跟普通函数的区别](/3671102502.html)， 这里还是很简单的 

```
obj.f1() 这个简单， 就是打印 obj
obj.f2() 这里，this 肯定不是打印 obj， 但是也不是 window ！！！ 而是一个空对象
new obj.f1() 这里 . 的执行优先级高于 new , 所以就是把 f1 这个函数当作一个构造函数， 去实例化了 f1, 所以， 这里的 this， 就是实例化之后的 f1 这个实例
从上边就知道了，
new obj.f2() 这个操作同样是去实例 f2 ，你是 f2 是一个箭头函数，他是不能当做构造函数的，所以这里就会报错

```
