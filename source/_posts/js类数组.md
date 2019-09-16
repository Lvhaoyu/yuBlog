---
title: js类数组
tags:
  - web前端
  - js基础
categories:
  - 技术日志
date: 2019-09-16 16:19:05
---


在`JavaScript`中，存在着一种对象，她们也叫做“类数组”，首先为了理解这个“类数组”的概念，就要从下面三个部分说起：

##  数组的概念：

> 数组是值的有序集合。每个值叫做一个元素，而每个元素在数组中有一个位置，以数字表示，称为索引。

同样是`js`中的引用类型，数组与其他的对象有什么不同？

> - 从`Array.prototype()`中继承一些特有的方法
> - 改变`length`属性会将数组截断或者增长
> - 类属性为`Array`
> - 数组中增加元素会自动改变`length`值

这四条中，有两条都和`length`有关，第一条和第三条有通用性，可以看出，js 中数组和其他引用类型最根本的区别就在于`length`属性上。

根据以上分析，《JavaScript 权威指南（原书第 6 版）》中有对于类数组的定义：

> 一种常常完全合理的看法把拥有一个数值 length 属性和对应非负整数属性的对象看做一种类型的数组。

大体意思就是将拥有`length`属性，且该属性的值为>=0 的整数，就可以称为类数组。

## 举例：

- 类数组：
  - [函数重载](https://www.lvhaoyu.cn/2019/09/03/js中的函数重载) 中提到的`arguments`对象
  - `DOM`方法中获取类或者元素的方法返回的类数组对象

```javascript
const a = {
  name: "tom",
  age: 18,
  length: 10
};
```

- 非类数组

```javascript
const a = {
  name: "tom",
  age: 18
};
```

## 检测类数组对象的方法：

```javascript
function isArrayLike(o) {
  if (
    o && //o非null、undefined等
    typeof o === "object" && //o是对象
    isFinite(o.length) && //o.length是有限数值
    o.length >= 0 && //o.length为非负值
    o.length === Math.floor(o.length) && //o.length是整数
    o.length < Math.pow(2,32)
  )
    //o.length＜2^32
    return true;
  //o是类数组对象
  else return false; //否则它不是
}
```

当然了，这里只考虑`length`的值是整数的情况，类数组之所以称为类数组，就是因为它和数组类似，但是不能直接使用数组方法.

注：
- 如果要显示出效果，该类数组的属性值最好写成数组索引形式，否则不会显示对应元素
- `length`的值为数字字符串时，和数字效果相同，但是在`isArrayLike`函数中会显示`false`（这个可能是犀牛书没有考虑到吧）

## 类数组对象方法：

类数组不能直接调用`Array.prototype`的方法，正常调用只能调用：

- `arrLike.length` 这个是当然的，因为当成对象来看就是访问了自己属性的值而已

- `arrLike[x]` （x是访问的索引），可以通过该方法访问单个元素
其实这个也可以通过对象来理解，对象有两种访问值的方法，一个是通过“ . + 属性”来访问，另一种就是通过“[]”来访问了

使用数组通用方法：

所谓的通用方法就是不强制要求函数的调用对象`this`必须为数组，仅需要其拥有 `length`属性和数字索引下标即可。

![](Jietu20190917-001420@2x.jpg)

可以看出类数组真正的核心在于`length`属性，一个对象有该属性，则可以生成对应长度的数组，没有则只能返回空。

## 类数组对象`key`的影响

![](Jietu20190916-234213.jpg)

可以看出，当索引是数字时，会根据索引大小排列元素，当索引为非数字时，会忽略该元素。

因为类数组不会关心除了数字索引和`length`属性之外的东西。

## 类数组转化为数组：

- 使用数组`Array.slice()`方法：

```javascript
Array.prototype.slice.call(arrLike)
```

- 使用数组`Array.from()`方法

```javascript
Array.from(arrLike)
```

该方法是专门用来将类数组对象转为数组的。

## 总结：

- 类数组就是有`length`属性，且为正整数（或可转换为正整数）的对象
- 类数组转为数组方法有两种，`Array.slice()`和`Array.from()`
- 如何检测类数组对象

## 参考文献

- [JavaScript 的怪癖 8：“类数组对象”](http://www.html-js.com/article/1619)
- [javascript 类数组](https://segmentfault.com/a/1190000000415572)