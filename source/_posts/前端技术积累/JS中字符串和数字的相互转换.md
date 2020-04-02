---
title: JS中字符串和数字的相互转换
tags:
  - web前端
categories:
  - 技术日志
  - JS基础
date: 2020-04-02 11:54:36
visible:
---


# JS 中字符串和数字的相互转换

今天遇到了这样一道题目，将 JS 中字符串和数字的转换很好的体现了出来。

![](Jietu20200402-115751.jpg)

题目大意是找出一个数字的二进制格式下面第一个拥有相同数目 1 的最大的字符串，

思路：

- 将数字转为二进制
- 找出二进制下 1 的数量
- 找出比该数字大且有相同 1 的数量的数字

## JS 中的进制转换

### JS 将数字转为字符串

在 JS 中，使用`toString()`方法来将数字转为相应的进制字符串。不能直接将数字转为另一进制的数字。而是需要进行数字&rarr;字符串&rarr;数字的转换。

在使用`toString`的过程中，我发现会出现下面的问题：

![](Jietu20200402-125030.jpg)

从报错信息可以知道，这是一个语法错误，表示解析器认为这个语句是非法的。这是因为在 JS 中，所有的数字都是以 IEEE754 双精度浮点数的标准存储的，所以`1234`在内部是以`1234.0`来存储。这样语句`1234.0.toString()`就无法解析了。

### JS 中将字符串转为数字

在`JS`中，字符串转数字可以使用`parseInt`，`parseFloat`，`Number()`来进行转换。涉及到进制转换就只能用`parseInt`。通过传入第二个参数来按照不同进制的规则解析。

使用`parseInt`应该注意，第二个参数最好是需要指定的，因为历史原因，有些解析器会把 0️ 开头的字符串转为默认 8 进制。所以第二个参数未指定的话，默认值是不可预料的。

## 答案

```js
function nextHigher(n) {
  var num_ones = (n.toString(2).match(/1/g) || []).length;
  while (true) {
    n++;
    if ((n.toString(2).match(/1/g) || []).length == num_ones) return n;
  }
}

function nextHigher(n) {
  let s = n.toString(2).replace(/0?1(1*)(0*)$/, "10$2$1");
  return parseInt(s, 2);
}
```

当然如果对位运算特别熟悉的话，可以使用下面这个方法：

```js
const nextHigher = (n, o = n & -n, v = n + o, p = ((n ^ v) / o) >> 2) =>
  n === 0 ? 0 : v | p;
```
