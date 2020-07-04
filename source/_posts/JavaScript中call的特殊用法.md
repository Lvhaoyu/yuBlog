---
title: JavaScript中call的特殊用法
tags:
  - web前端
categories:
  - 技术日志
  - JS基础
date: 2020-07-04 11:33:35
visible:
---


## 前言

在 JavaScript 中，`call`是一个很常见的方法，用来改变函数调用的时的`this`环境。

有这样一道题目：

```js
Number.call(Number, undefined, 0); // 等于什么？
Number.call.call(Number, undefined, 0); //等于什么？
```

## 分析知识点

### Number

`Number`作为内置的构造函数，在没有`new`的情况下，可以作为类型转换的方法，接受一个参数，将其转换为数字值。

用法如下：

```js
new Number(value); // 创建包装对象
var a = new Number('123'); // a === 123 is false
var b = Number('123'); // b === 123 is true
a instanceof Number; // is true
b instanceof Number; // is false
```

### call 方法

`call()`方法调用一个函数, 其具有一个指定的`this`值和分别地提供的参数。`call`的功能就是：

1. 把指定函数中的`this`指向`context`
2. 把指定函数执行

`call`方法内部的原理如下：

```js
Function.prototype.myCall = function (context) {
  context = context ? Object(context) : window;
  context.fn = this;
  let args = [...arguments].slice(1);
  let r = context.fn(args);
  delete context.fn;
  return r;
};
```

谁调用的方法，该方法的`this`就指向谁。`call`的`this`就是调用它的那个函数，而内部也只是将`this`指向传入的上下文，最后执行`this()`。

## 题目解答

```js
Number.call(Number, undefined, 0);
```

这个简化一下，就是`Number(undefined)`，结果是`NaN`。

```js
Number.call.call(Number, undefined, 0);
```

这个要分为两部分来看，第一部分是`Number.call`，第二部分是`call(Number, undefined, 0)`。

等价于`Number.call(undefined, 0)`，也就是 0。

## 扩展

```js
function func1() {
  console.log('func1执行', 1);
}

function func2() {
  console.log('func2执行', 2);
}

func1.call(func2); // 1
func1.call.call(func2) // 2
```

`func1.call.call(func2)`改变了第一个`call`内部的`this`，之前指向`func1`，如今指向`func2`。最后相当于`func2.call()`。

