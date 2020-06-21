---
title: JS 常见手撕手码题
abbrlink: 979881800
date: 2020-06-17 22:18:36
tags:
---

这种题目，除了多练，没别的方法

<!-- more -->

### 实现一个类似 emiter 的类

```js
class EventEmiter {
    constructor() {
        this.emiterMap = {};
    }

    on(event, callback) {
        if (this.emiterMap[event]) {
            this.emiterMap[event].push(callback);
        } else {
            this.emiterMap[event] = [callback];
        }
    }

    emit() {
        let [event, ...args] = [...arguments];
        this.emiterMap[event] && this.emiterMap[event].forEach((callback) => callback(...args));
    }

    off(event, callback) {
        this.emiterMap[event] = this.emiterMap[event].filter((cb) => cb != callback);
    }

    once(event, callback) {
        let fn = (...args) => {
            callback(args);
            this.off(event, fn);
        };
        this.on(event, fn);
    }
}

let em = new EventEmiter();

var work1 = function () {
    console.log('work1');
};
var work2 = function () {
    console.log('work2');
};
var once1 = function () {
    console.log('once1');
};

em.on('work1', work1);
em.on('work2', work2);

em.emit('work1');
em.emit('work1');
em.emit('work2');
em.emit('work2');

em.once('once', once1);

em.emit('once');
em.emit('once');
em.emit('once');
em.emit('once');
em.emit('once');
em.emit('once');

var day = 18;
em.on('addday', function () {
    let [...args] = [...arguments];
    console.log('in add arg: ', args[0], args[1]);
    day = day + Number(args[0]) + Number(args[1]);
    console.log('add day: ', day);
});

em.on('subday', function () {
    day = day - 5;
});

em.on('logday', function () {
    console.log(day);
});
em.emit('addday', 10, 20);
em.emit('subday');
em.emit('logday');
```

### 函数柯里化

```js
function add() {
    var _args = Array.prototype.slice.call(arguments);

    // 利用闭包不会释放函数里边的变量特性，把新的参数再push到原来的参数里边
    var _addr = function () {
        _args.push(...arguments);
        return _addr;
    };

    // 利用每次输出值的时候， 会自动调toString的方法， 在这里重写一下toString
    _addr.toString = () => _args.reduce((total, num) => total + num);

    return _addr;
}

console.log(add(1, 2, 3)(4)(5));
```

### 快速排序

```js
function quickSort(arr) {
    if (arr.length <= 1) {
        return arr;
    }
    var pivotIdx = Math.floor(arr.length / 2);

    var pivot = arr.splice(pivotIdx, 1)[0];

    var left = [];
    var right = [];

    for (let index = 0; index < arr.length; index++) {
        const element = arr[index];
        if (element < pivot) {
            left.push(element);
        } else {
            right.push(element);
        }
    }

    return quickSort(left).concat([pivot], quickSort(right));
}

console.log(quickSort([2, 4, 1, 7, 3, 5]));
```

### 实现一个 calc 方法，可以将输入的数拆解为尽可能多的乘数，所有数相乘等于输入数。

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
            return false;
        }
    }
    return true;
}

function calc(n) {
    let list = [];
    while (n > 1) {
        for (let i = 2; i <= n; i++) {
            if (isPrime(i) && n % i === 0) {
                list.push(i);
                n /= i;
            }
        }
    }
    return list;
}

console.log(calc(2));
// [2]

console.log(calc(8));
// [2, 2, 2]

console.log(calc(24));

// [2, 2, 2, 3]

console.log(calc(30));
// [2, 3, 5]
```

### 多维数组扁平化

```js
function flatDeep(arr) {
    return arr.reduce(
        (acc, curr) => (Array.isArray(curr) ? acc.concat(flatDeep(curr)) : acc.concat(curr)),
        []
    );
}
let a = [1, 2, 3, [4, 5, 6, [7, 8], 9], 10];
console.log(flatDeep(a));
```

### 击鼓传花

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
];

class Queue {
    constructor() {
        this.queueList = [];
    }

    addItem(item) {
        this.queueList.push(item);
    }

    delItem(item) {
        return this.queueList.shift();
    }

    size() {
        return this.queueList.length;
    }
}

function findPepole(arr, num) {
    let myQueue = new Queue();
    for (let i = 0; i < arr.length; i++) {
        const element = arr[i];
        myQueue.addItem(element);
    }

    let eliminated = '';
    while (myQueue.size() > 1) {
        // 这里相当于把你设定的长度减去一个放到了这个队列的后边
        for (let i = 0; i < num; i++) {
            myQueue.addItem(myQueue.delItem()); //数组出队然后入队
        }
        // 这里就是把设定长度的那个位置的那一个删除了
        eliminated = myQueue.delItem();

        console.log(eliminated + ' 在击鼓传花游戏中被淘汰。');
    }
    return myQueue.queueList;
}

let victor = findPepole(players, 3)[0];
console.log(`胜利者是： ${victor}`);
```

> 主要思路就是， 一个一个删，如果删除的那一个不是指定位置的那一个，就放到最后边，如果是， 就直接删除，直到数组只有一个了
