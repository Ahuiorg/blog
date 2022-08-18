---
title: watch
abbrlink: 1342917158
date: 2022-07-12 16:41:19
tag: vue3,watch,watchEffect
categories: vue3
---

### [vue3中 watch、watchEffect区别](https://segmentfault.com/a/1190000039916178)

1、watch是惰性执行，也就是只有监听的值发生变化的时候才会执行，但是watchEffect不同，每次代码加载watchEffect都会执行（忽略watch第三个参数的配置，如果修改配置项也可以实现立即执行）

2、watch需要传递监听的对象，watchEffect不需要

3、watch只能监听响应式数据：ref定义的属性和reactive定义的对象，如果直接监听reactive定义对象中的属性是不允许的，除非使用函数转换一下

4、watchEffect如果监听reactive定义的对象是不起作用的，只能监听对象中的属性。

```javascript
let count = ref(0)
let countObj = reactive({count: 0})

// 惰性，首次加载不执行
watch(count, (newVal, oldVal) =>{console.log(newVal, oldVal)} )
// watch 不能直接监听reactive里面的属性，只能监听ref、reactiveObject， function， array, 如果想监听reactive的某个属性，那么需要转换成函数
watch(() => countObj.count, (newVal, oldVal) => {console.log(oldVal, newVal)}, {})
watch (countObj, (newVal, oldVal) => {
  console.log(newVal, oldVal)
})
// 监听多个值，前面是监听数据的数组，后面的参数是两个数组，前面数组是变化后监听对象值的数组，后面是变化前监听对象值的数组
watch ([countObj, count], ([oneNewName, twoNewName], [oneOldName, twoOldName]) => {
  console.log(oneNewName, oneOldName, twoNewName, twoOldName)
})
// watchEffect，和watch不一样，1、会立即执行，只要定义了就会执行。2、他只能监听某个值，监听对象不管用。3、不需要传递参数，会自动管制代码中的变量。4、没法获取newVal和oldVal
const watchEf = watchEffect(() => {
  console.log(countObj.count)
})
```





参考

[原文地址](https://segmentfault.com/a/1190000039916178)
