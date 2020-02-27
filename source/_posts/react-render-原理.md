---
title: react render 原理
tags:
  - react
  - js
abbrlink: 2202868307
date: 2020-02-20 12:31:43
---

JSX 代码经过 babel 编译之后变成 React.createElement 的表达式，这个表达式在 render 函数被调用的时候执行生成一个 element。

在首次渲染的时候，先去按照规则初始化 element，接着 ReactComponentComponentWrapper 通过递归，最终调用 ReactDOMComponent 的 mountComponent 方法来帮助生成真实 DOM 节点。

地址一： [React 从渲染原理到性能优化（一）](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=2651229767&idx=1&sn=8f06283e43cfcda722189b56644f4dfc&chksm=bd4957c38a3eded58cd388130c4f303ff4033213ffcf157698d50f1ebfe87788a7f74d8be76a&scene=21#wechat_redirect)

地址二： [React 从渲染原理到性能优化（二）-- 更新渲染](https://mp.weixin.qq.com/s/aM-SkTsQrgruuf5wy3xVmQ)
