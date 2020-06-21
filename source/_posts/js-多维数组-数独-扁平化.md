---
title: js 多维数组(数独)扁平化
abbrlink: 1909019848
date: 2020-06-07 12:25:27
tags:
---

[1, 2, 3, [4, 5, 6, [7, 8], 9], 10] => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

<!-- more -->

### reduce + concat
```js
    function flattenDeep(arr) {
        return arr.reduce((acc, curr) => Array.isArray(curr) ? acc.concat(flattenDeep(curr)) : acc.concat(curr), [])
    }

    let a = [1, 2, 3, [4, 5, 6, [7, 8], 9], 10]
    console.log(flattenDeep(a))
```

### toString
```js
    function flattenDeep(arr) {
        return arr.toString().split(",").map(item => Number(item))
    }

    let a = [1, 2, 3, [4, 5, 6, [7, 8], 9], 10]
    console.log(flattenDeep(a))
```
> 这是什么高级方法 ！！！

### forEach
```js
    function flattenDeep(arr) {
        let res = []
        arr.forEach(item => {
            if (Array.isArray(item)) {
                res = res.concat(flattenDeep((item)))
            } else {
                res.push(item)
            }
        })
        return res
    }

    let a = [1, 2, 3, [4, 5, 6, [7, 8], 9], 10]
    console.log(flattenDeep(a))
```
> 原理跟reduce的一样， 应该说 reduce 的原理跟这个一样



