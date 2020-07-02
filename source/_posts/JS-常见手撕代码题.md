---
title: JS 常见手撕代码题
abbrlink: 979881800
date: 2020-06-17 22:18:36
tags:
---

这种题目，除了多练，没别的方法

<!-- more -->

### 1. 实现一个类似 emiter 的类

```js
class EventEmiter {
  constructor() {
    this.emiterMap = {}
  }

  on(event, callback) {
    if (this.emiterMap[event]) {
      this.emiterMap[event].push(callback)
    } else {
      this.emiterMap[event] = [callback]
    }
  }

  emit() {
    let [event, ...args] = [...arguments]
    this.emiterMap[event] && this.emiterMap[event].forEach((callback) => callback(...args))
  }

  off(event, callback) {
    this.emiterMap[event] = this.emiterMap[event].filter((cb) => cb != callback)
  }

  once(event, callback) {
    let fn = (...args) => {
      callback(args)
      this.off(event, fn)
    }
    this.on(event, fn)
  }
}

let em = new EventEmiter()

var work1 = function () {
  console.log('work1')
}
var work2 = function () {
  console.log('work2')
}
var once1 = function () {
  console.log('once1')
}

em.on('work1', work1)
em.on('work2', work2)

em.emit('work1')
em.emit('work1')
em.emit('work2')
em.emit('work2')

em.once('once', once1)

em.emit('once')
em.emit('once')
em.emit('once')
em.emit('once')
em.emit('once')
em.emit('once')

var day = 18
em.on('addday', function () {
  let [...args] = [...arguments]
  console.log('in add arg: ', args[0], args[1])
  day = day + Number(args[0]) + Number(args[1])
  console.log('add day: ', day)
})

em.on('subday', function () {
  day = day - 5
})

em.on('logday', function () {
  console.log(day)
})
em.emit('addday', 10, 20)
em.emit('subday')
em.emit('logday')
```

### 2. 函数柯里化

```js
function add() {
  var _args = Array.prototype.slice.call(arguments)

  // 利用闭包不会释放函数里边的变量特性，把新的参数再push到原来的参数里边
  var _addr = function () {
    _args.push(...arguments)
    return _addr
  }

  // 利用每次输出值的时候， 会自动调toString的方法， 在这里重写一下toString
  _addr.toString = () => _args.reduce((total, num) => total + num)

  return _addr
}

console.log(add(1, 2, 3)(4)(5))
```

### 3. 快速排序

```js
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr
  }
  var pivotIdx = Math.floor(arr.length / 2)

  var pivot = arr.splice(pivotIdx, 1)[0]

  var left = []
  var right = []

  for (let index = 0; index < arr.length; index++) {
    const element = arr[index]
    if (element < pivot) {
      left.push(element)
    } else {
      right.push(element)
    }
  }

  return quickSort(left).concat([pivot], quickSort(right))
}

console.log(quickSort([2, 4, 1, 7, 3, 5]))
```

### 4. 实现一个 calc 方法

可以将输入的数拆解为尽可能多的乘数，所有数相乘等于输入数。

```js
/**
 * @param {number} num 乘积
 * @return {Array} 拆解后的乘数
 */

// function calc(num) {
//     function calc(num) {
//         const arr = [];
//         var i = 2;
//         while (i <= num) {
//             if (num % i === 0) {
//                 arr.push(i);
//                 num = num / i;
//             } else {
//                 i++;
//             }
//         }
//         return arr;
//     }
// }

function isPrime(num) {
  for (let i = 2; i <= Math.sqrt(num); i++) {
    if (num % i == 0) {
      return false
    }
  }
  return true
}

function calc(n) {
  let list = []
  while (n > 1) {
    for (let i = 2; i <= n; i++) {
      if (isPrime(i) && n % i === 0) {
        list.push(i)
        n /= i
      }
    }
  }
  return list
}

console.log(calc(2))
// [2]

console.log(calc(8))
// [2, 2, 2]

console.log(calc(24))

// [2, 2, 2, 3]

console.log(calc(30))
// [2, 3, 5]
```

### 5. 多维数组扁平化

```js
function flatDeep(arr) {
  return arr.reduce(
    (acc, curr) => (Array.isArray(curr) ? acc.concat(flatDeep(curr)) : acc.concat(curr)),
    []
  )
}
let a = [1, 2, 3, [4, 5, 6, [7, 8], 9], 10]
console.log(flatDeep(a))
```

### 6. 击鼓传花

```js
let players = [
  'n1',
  'n2',
  'n3',
  'n4',
  'n5',
  'n6',
  'n7',
  'n8',
  'n9',
  'n10',
  'n11',
  'n12',
  'n13',
  'n14',
  'n15',
]

class Queue {
  constructor() {
    this.queueList = []
  }

  addItem(item) {
    this.queueList.push(item)
  }

  delItem(item) {
    return this.queueList.shift()
  }

  size() {
    return this.queueList.length
  }
}

function findPepole(arr, num) {
  let myQueue = new Queue()
  for (let i = 0; i < arr.length; i++) {
    const element = arr[i]
    myQueue.addItem(element)
  }

  let eliminated = ''
  while (myQueue.size() > 1) {
    // 这里相当于把你设定的长度减去一个放到了这个队列的后边
    for (let i = 0; i < num; i++) {
      myQueue.addItem(myQueue.delItem()) //数组出队然后入队
    }
    // 这里就是把设定长度的那个位置的那一个删除了
    eliminated = myQueue.delItem()

    console.log(eliminated + ' 在击鼓传花游戏中被淘汰。')
  }
  return myQueue.queueList
}

let victor = findPepole(players, 3)[0]
console.log(`胜利者是： ${victor}`)
```

> 主要思路就是， 一个一个删，如果删除的那一个不是指定位置的那一个，就放到最后边，如果是， 就直接删除，直到数组只有一个了

### 7. 两个数组求交集

```js
let list1 = [1, 2, 3, 4, 4, 5]
let list2 = [4, 4, 5, 6, 7, 8]

function intersection(arr1, arr2) {
  let tempList = []
  for (let i = 0; i < arr1.length; i++) {
    const element = arr1[i]
    if (arr2.includes(element)) {
      tempList.push(element)
    }
  }

  return tempList

  // return arr1.filter(item => arr2.includes(item));
  // return arr1.filter(item => new Set(arr2).has(item));
}

console.log(intersection(list1, list2))
```

### 8. 求字符串数组的最长公共前缀

比如输入: ["flower","flow","flight"]，输出: "fl"

```js
let longestCommonPrefix = function (strs) {
  if (strs.length === 0) return ''
  if (strs.length === 1) return strs[0]
  let tempStr = strs[0]
  for (let i = 0; i < strs.length; i++) {
    let j = 0
    for (; j < strs.length && j < tempStr.length; j++) {
      if (tempStr.charAt(j) !== strs[i].charAt(j)) {
        break
      }
    }
    tempStr = tempStr.substring(0, j)
  }
  return tempStr
}

let commonStr = longestCommonPrefix(['flower', 'flow', 'flight'])
console.log(commonStr)
```

### 9. 合并两个数组

```js
// 合并两个数组

let a = [1, 2, 3]
let b = [4, 5, 6]

// concat
let c = a.concat(b)
console.log(c, a, b)

// // apply
// Array.prototype.push.apply(a, b)
// console.log(a)

// for
// for (let i = 0; i < b.length; i++) {
//     a.push(b[i])
// }
// console.log(a);

// // ...
// c =[...a, ...b]
// console.log(c);
```

### 10. 实现一个PromiseLinit

根据 urls 数组内的 url 地址进行并发网络请求，最大并发数 maxNumber,当所有请求完毕后调用 callback 函数

```js
function fetch(url) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve('ok ' + url)
    }, Math.floor(Math.random() * 1000) + 600)
  })
}

function PromiseLinit(urls, limit, callback) {
  let result = []
  let urlsLen = urls.length
  let i = 0

  while (i < limit) {
    next()
  }

  function next() {
    let current = i++
    if (current >= urlsLen) {
      result.length === urlsLen && callback(result)
      return
    }

    console.log(`${current + 1}开始`)
    fetch(urls[current])
      .then((res) => {
        console.log(`${current + 1}成功结束`)
        result.push(res)
        if (current < urlsLen) {
          next()  
        }
      })
      .catch((err) => {
        console.log(`${current + 1}失败结束`)
        result.push(res)
        if (current < urlsLen) {
          next()
        }
      })
  }
}

let urls = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]

PromiseLinit(urls, 4, (res) => {
  console.log('success: ', res)
})

```

### 11. 求 left， right 最终宽度

#### 11.1 算收缩

```HTML
<!DOCTYPE html>
<html lang="en">
  <body>
    <div class="container">
      <div class="left"></div>
      <div class="right"></div>
    </div>
  </body>

  <style>
    * {
      padding: 0;
      margin: 0;
    }
    .container {
      width: 600px;
      height: 300px;
      display: flex;
    }
    .left {
      flex: 1 2 500px;
      background: red;
    }
    .right {
      flex: 2 1 400px;
      background: blue;
    }
  </style>
</html>
```

> 子项的收缩宽度 = 子项收缩比例 X 溢出宽度
> 子项收缩比例 = 子项宽度 X 子项收缩系数 / 所有子项的(宽度 X 系数)之和

对应题目：

子项溢出空间的宽度为 $500 + 400 - 600 = 300$
left 收缩比例：$(500 × 2) ÷ (500 × 2 + 400 × 1) ≈ 0.7143$
right 收缩比例：$(400 × 1) ÷ (500 × 2 + 400 × 1) ≈ 0.2857$
对应的：

left 收缩宽度：$0.7143 × 300 = 214.29$
right 收缩宽度：$0.2857 × 300 = 85.71$
所以：

left 最终宽度：$500 - 214.29 = 285.71$
right 最终宽度：$400 - 85.71 = 314.29$

#### 11.2 算扩展

```HTML
<!DOCTYPE html>
<html lang="en">
  <body>
    <div class="container">
      <div class="left"></div>
      <div class="right"></div>
    </div>
  </body>

  <style>
    * {
      padding: 0;
      margin: 0;
    }
    .container {
      width: 600px;
      height: 300px;
      display: flex;
    }
    .left {
      flex: 1 2 300px;
      background: red;
    }
    .right {
      flex: 2 1 200px;
      background: blue;
    }
  </style>
</html>

```

left 最终宽度：300 + (1/3) _ (600 - (300 + 200)) = 333.33
right 最终宽度：200 + (2/3) _ (600 - (300 + 200)) = 266.66

### 12. 实现一个 promise.all

```js
// 1. 参数： 数组， 数项是promise返回执行结果， 如果不是， 直接返回
// 2. 返回值： Promise 对象
// 3. 如果数组里边有一个 Promise rejected 了， 直接返回 rejected
// 4. 当全部执行成功后， 返回一个有所有执行结果的数组

function isPromise(obj) {
  return (
    obj && (typeof obj === 'object' || typeof obj === 'function') && typeof obj.then === 'function'
  )
}

function myPromiseAll(arr) {
  let requests = []
  return new Promise((resolve, reject) => {
    arr.map((item, idx) => {
      if (isPromise(item)) {
        item
          .then((result) => {
            requests[idx] = result
            if (requests.length === arr.length) {
              resolve(requests)
            }
          })
          .catch((err) => {
            reject(err)
          })
      } else {
        requests[idx] = item
      }
    })
  })
}

let p1 = Promise.resolve(3)
let p2 = 1337
let p3 = new Promise((resolve, reject) => {
  setTimeout(resolve, 100, 'foo')
})

myPromiseAll([p1, p2, p3]).then((values) => {
  console.log(values) // [3, 1337, "foo"]
})
```
