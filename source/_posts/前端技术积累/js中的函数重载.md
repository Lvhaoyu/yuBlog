---
title: JS中的函数重载
date: 2019-09-03 21:37:37
tags:
- web前端
categories:
- 技术日志
- JS基础
---

>重载函数是函数的一种特殊情况，为方便使用，C++允许在同一范围中声明几个功能类似的同名函数，但是这些同名函数的形式参数（指参数的个数、类型或者顺序）必须不同，也就是说用同一个函数完成不同的功能。 这就是重载函数。 重载函数常用来实现功能类似而所处理的数据类型不同的问题。 不能只有函数返回值类型不同。

JS中的函数可以说是JS的“一等公民”了，要不为什么`typeof`就能识别出来函数是`function`呢，对于函数的探索还是有很长的一段路要走，今天就唠一唠JS中函数如何实现重载。

## JS函数可以实现重载吗？
在JS高级程序设计中明确的指出了JS函数式不能实现传统意义上的重载的，因为JS的参数是由包含0个或者多个参数的类数组的形式来表示的，所以不能像C++那样通过函数签名不同来实现重载。

但是JS是可以实现特殊意义上的重载的，上边说了JS的参数是放在一个类数组中传递的，那么自然会有一个所谓的类数组来存储这些参数，这个类数组对象就是`arguments`，`arguments`里面保存着该函数所有的参数，所以就算你写的参数中仅仅需要传递两个实参，你传十个八个还是可以运行的，只不过多余参数的是对应不上的相应的形参的。

```javascript
function argu(a, b) {
  console.log(arguments[0]); //  1
  console.log(arguments[1]); //  2
  console.log(arguments[2]); //  3
  console.log(arguments[3]); //  4
  console.log(arguments[4]); //  5
  console.log(arguments[5]); //  undefined  
  return a + b;
}

console.log(argu(1,2,3,4,5));  // 3
```

为什么说`arguments`是一个类数组呢？因为他虽然有`length`属性，也可以通过下标来访问该对象之中的元素，但是，数组方法它是不支持的。

![](Jietu20190903-220022.jpg)

但是在严格模式下，`arguments`是不起作用的。

![](Jietu20190903-221001.jpg)

下面讲一下JS函数的重载实现，注意：JS是没有严格意义上的重载的。

首先，可以利用`arguments`的`length`属性，实现JS函数的重载。具体实现大概就是在函数中使用`switch-case-break`来根据长度的不同，实现不同的操作，不过这个就显low了。

在jQuery之父的博客里面，有这个问题的另一个答案，他是这样实现的：

首先明白几个概念：

- 函数的长度是函数需要的参数的个数，`arguments`的长度是实际传给函数的参数的个数
- this的指向
  - 情况1：如果一个函数中有this，但是它没有被上一级的对象所调用，那么this指向的就是`window`，这里需要说明的是在JS的严格版中this指向的不是`window`，但是这里不探讨严格版的问题。
  - 情况2：如果一个函数中有this，这个函数有被上一级的对象所调用，那么this指向的就是上一级的对象。
  - 情况3：如果一个函数中有this，这个函数中包含多个对象，尽管这个函数是被最外层的对象所调用，this指向的也只是它上一级的对象
  - this永远指向的是最后调用它的对象，也就是看它执行的时候是谁调用的。

实现：

有一个对象里面存着一些姓名：

```javscript
var people = {
  values: ["Dean Edwards", "Sam Stephenson", "Alex Russell", "Dean Tom"]
};
```
目的是要实现一个方法，当传入一个参数，返回匹配参数姓氏的名字，两个返回匹配参数的全名，没传参数则返回整个对象的`values`：

```javascript
addMethod(people, "find", function() {}); /*不传参*/
addMethod(people, "find", function(a) {}); /*传一个*/
addMethod(people, "find", function(a, b) {}); /*传两个*/
```

下面是jQuery作者实现的方法：

- `addMethod`函数接收3个参数，第一个为要绑定方法的对象，第二个为绑定的方法名称，第三个为需要绑定的方法（一个匿名函数）
- 这个函数的功能是将方法名以及对应的方法绑定到传入的对象上

```javascript
function addMethod (object, name, fn) {
  // 先把原来的object[name] 方法，保存在old中
  var old = object[name];

  // 重新定义 object[name] 方法
  object[name] = function () {
    // 如果函数需要的参数（fn.length）和 实际传入的参数(arguments.length) 的个数相同，就直接调用fn
    if (fn.length === arguments.length) {
      // 改变this指向，将fn添加到object(也就是下文的people对象)环境中
      return fn.apply(this, arguments);

      // 如果不相同,判断old 是不是函数，
      // 如果是就调用old，也就是刚才保存的 object[name] 方法
    } else if (typeof old === "function") {
      // 这个this就是函数执行环境的这个对象people
      return old.apply(this, arguments);
    }
  }
}
```
- 下面就是对传入参数中函数的实现

```javascript
// 不传参数时，返回peopld.values里面的所有元素
addMethod(people, "find", function() {
　　return this.values;
});
 
// 传一个参数时，按first-name的匹配进行返回
addMethod(people, "find", function(firstName) {
　　var ret = [];
　　for(var i = 0; i < this.values.length; i++) {
　　　　if(this.values[i].indexOf(firstName) === 0) {
　　　　　　ret.push(this.values[i]);
　　　　}
　　}
　　return ret;
});
 
// 传两个参数时，返回first-name和last-name都匹配的元素
addMethod(people, "find", function(firstName, lastName) {
　　var ret = [];
　　for(var i = 0; i < this.values.length; i++) {
　　　　if(this.values[i] === (firstName + " " + lastName)) {
　　　　　　ret.push(this.values[i]);
　　　　}
　　}
　　return ret;
});
 
// 测试：
console.log(people.find()); //["Dean Edwards", "Alex Russell", "Dean Tom"]
console.log(people.find("Dean")); //["Dean Edwards", "Dean Tom"]
console.log(people.find("Dean Edwards")); //["Dean Edwards"]
```

## 分析

`addMethod`函数很巧妙，巧妙到什么程度呢？一般人根本想不到这么实现...

首先，该方法利用了JS中闭包的特点，在内层函数中调用外层变量（old），这样所有的函数就都会在内存中不被销毁，所以这些同名的函数才会同时存在，因为这三个之间是有联系的啊，至于是靠什么将他们联系在一起的呢？就是这句代码了：

```javascript
var old = object[name];
```
借用一下网上找的图：

![](Jietu20190904-153408.jpg)

我们知道`object[name]`是一个函数，这个函数中使用了`old`这个变量。
- 第一次执行的时候，`old`是`undefined`，这个很容易理解，因为还没定义赋值的内容哪里来的值，之后开始对`object[name]`赋值，这时候编译器就知道了，`old`不能删！
- 第二次执行的时候，`old`就是上一次`object[name]`的内容了（find之中未传参数），之后继续执行，发现`old`又被改变了。
- 之后就是第三次执行，这个时候`old`就是上一次的`object[name]`的内容。

发现了吗？这个执行过程是一环套一环的，每一个都会调用上一个，形成了闭包，所以每一次数据都会保留在内存中，当调用的时候，就会先调用最后一次的执行结果，发现参数对上了，就直接执行，没对上，就会顺着`old`往上走，直到找到对应参数个数符合的函数。



## 总结 

- 其实`splice()`，`parseInt()`都用到了重载
- 重载的好处就是一个函数可以实现多种功能，为功能实现时的调用提供便利

## 参考

- JavaScript忍者秘籍第一版
- [JavaScript中的函数重载(Function overloading)](https://juejin.im/post/5b4465516fb9a04fe017f926#heading-4)
- [浅谈JavaScript函数重载](https://www.cnblogs.com/yugege/p/5539020.html)
- [JavaScript函数重载](https://segmentfault.com/a/1190000010425095)
- [JavaScript Method Overloading](https://johnresig.com/blog/javascript-method-overloading/)
