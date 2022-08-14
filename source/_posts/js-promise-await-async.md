---
uuid: 49c30aee-120c-11ed-aacc-b11deac0aae6
title: js Promise / Generator / await async
abbrlink: 464566509
date: 2020-03-03 23:02:31
tags:
---

**Promise 是用来解决函数回调嵌套的， async、await 是用来解决逻辑上的函数依赖的。**

<!-- more -->

`Promise`是一个有状态的对象，用来规范回调函数，内容是一个`function(function Promise() { [native code] })`，内部接收一个`function`，参数为`resolve`，`reject`，用于处理耗时操作的等待。

```js
let promise = new Promise(function(resolve,reject){
  if (condition){
    resolve(...);
  } else {
    reject(...);
  }
});

promise
.then((result) => {
})
.catch((err) =>{
})
```

三种状态：`pending`（进行中）、`fulfilled`（已成功）和 `rejected`（已失败）

处理成功，调用`resolve`方法，状态会从 `pending` 变成 `fulfilled`
处理失败，调用`reject`方法，状态会从 `pending` 变成 `rejected`
一旦状态改变，就不会再变，任何时候都可以得到这个结果

**一般注意几点**

1. `.then` 可以无限链, 因为每一个 `.then` 都是返回一个 `promise` 对象
2. 当 `.then` 有第二个参数的时候，就算遇到报错，后边 `.catch` 里边的内容不会再执行
3. `.then` 里边如果有 return 一个新 `Promise` 的话，后面的 `.then` 是新 `Promise` 的返回结果， 看一下下边代码

```js
new Promise((resolve, reject) => {
    console.log(1);
    setTimeout(() => {
        console.log(2);
    });
    resolve(2);
})
    .then((res) => {
        console.log(3, res); // 2
        return 3;
    })
    .then((res) => {
        console.log(4, res); // 3
        return new Promise((resolve, reject) => {
            console.log(4);
            resolve(4);
        }).then((res) => {
            console.log(5, res); // 4
            return 5;
        });
    })
    .then((res) => {
        console.log(6, res); // 5
    });
```

**Generator 函数**

Generator 函数有多种理解角度。语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态。

执行 Generator 函数会返回一个遍历器对象，也就是说，Generator 函数除了状态机，还是一个遍历器对象生成函数。返回的遍历器对象，可以依次遍历 Generator 函数内部的每一个状态。

Generator 函数是一个普通函数，但是有两个特征。一是，function关键字与函数名之间有一个星号；二是，函数体内部使用yield表达式，定义不同的内部状态（yield在英语里的意思就是“产出”）。

```js
function* helloWorldGenerator() {
  yield 'hello';
  yield 'world';
  return 'ending';
}

var hw = helloWorldGenerator();
```
然后，Generator 函数的调用方法与普通函数一样，也是在函数名后面加上一对圆括号。不同的是，调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）。

下一步，必须调用遍历器对象的next方法，使得指针移向下一个状态。也就是说，每次调用next方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个yield表达式（或return语句）为止。换言之，Generator 函数是分段执行的，yield表达式是暂停执行的标记，而next方法可以恢复执行。

```js
hw.next()
// { value: 'hello', done: false }

hw.next()
// { value: 'world', done: false }

hw.next()
// { value: 'ending', done: true }

hw.next()
// { value: undefined, done: true }
```

**async函数就是将 Generator 函数的星号（*）替换成async，将yield替换成await，仅此而已**

比 Generator 的好处

1. 内置执行器
2. 更好的语义
3. 更广的适用性
4. 返回值是 Promise

```js
async function getStockPriceByName(name) {
  const symbol = await getStockSymbol(name);
  const stockPrice = await getStockPrice(symbol);
  return stockPrice;
}

getStockPriceByName('goog').then(function (result) {
  console.log(result);
});
```