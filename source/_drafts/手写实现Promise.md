---
title: 手写实现 Promise
date: 2020-03-06 13:08:42
tags:
  - web前端
categories:
  - 技术日志
  - JS基础
visible:
---

# 手写 Promise

Promise 是异步编程的解决方案，之前通过回调函数和事件来解决，相比与之前的解决方案，Promise 更加强大，也更加语义化。

## Promise 的概念

Promise 就是承诺的意思，它是一个容器，里面保存着某个未来才会结束事件的结果。可以从 Promise 获得异步操作的信息。

承诺有三种状态，等待中（pending），完成了（fulfilled），失败了（rejected）。一旦状态更改就不能改变了，三个状态对应着两个过程，等待到完成是`resolve`，等待到失败是`reject`。承诺一旦从等待状态变为其他状态就永远不能更改状态了，使用构造函数构造 Promise 时，构造函数的内部代码是立即执行。同时 Promise 也提供了统一的 API 来供使用者调用。

## 手写 Promise 实现

### 简易版本

首先要定义三个状态，函数内部还要有两个过程。
