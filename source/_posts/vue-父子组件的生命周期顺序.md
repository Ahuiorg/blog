---
uuid: 49c30af5-120c-11ed-aacc-b11deac0aae6
title: vue 父子组件的生命周期顺序
abbrlink: 677899705
date: 2020-07-04 00:18:42
tags: [vue, vue 原理]
---

### 加载渲染过程

<!-- more -->

#### 同步加载

父组件 beforeCreate,  created, beforMount
子组件 beforeCreate, created, beforMount
孙子组件 beforeCreate, created, beforMount
孙子组件 mounted
子组件 mounted
父组件 mounted


#### 异步加载

父组件 beforeCreate, created, beforMount, mounted
子组件 beforeCreate, created, beforMount, mounted


### 更新过程

父组件 beforeUpdate 
子组件 beforeUpdate
子组件 updated
父组件 updated

### 销毁过程
父组件 beforeDestroy
子组件 beforeDestroy
子组件 destroyed
父组件 destroyed

