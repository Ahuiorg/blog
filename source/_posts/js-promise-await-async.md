---
title: js promise await async
abbrlink: 464566509
date: 2020-03-03 23:02:31
tags:
---

<!-- more -->

```js
const promise = new Promise((resolve, reject) =>{
  setTimeout(() => {
    resolve(2000)
  }, 2000)
}).then(res => {
  console.log(res, 1)

  return Promise.resolve(1000)

  return Promise,resolve(3000)

}).then(res => {
  console.log(res, 2) 
})
```

```js

const demo = async function () {
  return "hello"
}

demo().then(res => {
  console.log(res)
})

```