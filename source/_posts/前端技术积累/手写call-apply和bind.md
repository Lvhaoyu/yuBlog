---
title: '手写call,apply和bind'
tags:
  - web前端
categories:
  - 技术日志
  - JS基础
date: 2020-03-07 12:01:27
visible:
---

# 手写 call，apply，bind

这三个方法都和`this`息息相关，`this`很简单，是函数的执行上下文，永远指向最后调用它的那个对象。

`call`，`apply`，`bind`都是用来改变`this`指向的，改变`this`除了这三种方法，还可以使用箭头函数，内部使用将`this`赋值给另一个变量，使用`new`实例化对象。

因为箭头函数没有`this`，如果说它有`this`，那么它的`this`永远指向定义时的词法环境。箭头函数默认不会使用自己的`this`，而是会和外层的`this`保持一致，最外层的`this`就是`window`对象。多层对象嵌套里箭头函数里`this`是和最最外层保持一致的。

## call，apply，bind 的区别

`call`和`apply`都是直接给函数指定一个`this`，具体语法如下。

```js
function.call(thisArg, arg1, arg2, ...)
fun.apply(thisArg, [argsArray]);
```

二者的区别就是`call`传入参数列表，`apply`传入参数数组。

`bind`和它们不同，`bind`创建一个新的函数，在`bind`被调用时，新函数的`this`被指定为`bind`第一个参数，其余作为新函数的参数。使用`bind`需要手动调用。

```js
function.bind(thisArg[, arg1[, arg2[, ...]]])
```

以上方法如果不指定第一个参数那么就会默认指向全局对像。

## 实现 call

### 先把函数框架写好

```js
Function.prototype.myCall = function(context) {};
```

### 验证调用该方法的对象是不是参数

```js
if (typeof this !== 'fucntion') {
  throw new TypeError('参数不对');
}
```

### 指定默认上下文

```js
context = context || window;
```

### 将参数（this）绑定到 fn 上

```js
context.fn = this;
```

### 将 call 传进来的参数传入 fn 并调用

```js
context.fn([...arguments].slice(1));
```

这里面`slice(1)`是创建一个参数的浅拷贝，并舍去第一个参数，因为第一个参数是传进的`this`和调用`call`的函数。

### 删除 context 的 fn 属性

```js
delete context.fn;
```

### 全部

```js
Function.prototype.myCall = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error');
  }
  context = context || window;
  context.fn = this;
  const args = [...arguments].slice(1);
  const result = context.fn(...args);
  delete context.fn;
  return result;
};
```

## 实现 bind

`bind`和`call`的区别在于该方法第二个参数是一个数组。而且该方法有且只有两个参数。

```js
Function.prototype.myBind = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error');
  }
  context = context || window;
  context.fn = this;
  const result = arguments[1] ? context.fn(arguments[1]) : context.fn();
  delete context.fn;
  return result;
};
```

## 实现 bind

首先要将`this`拿出来，也就是调用该方法的函数。

```js
let _this = this;
```

之后由于`bind`返回的是一个函数，这个函数可以直接调用，也可以通过`new`的方式调用。如果使用`new`来调用，那么内部会发生以下操作。

- 新建一个对象
- 将构造函数的作用域添加给这个对象，也就是让`this`（就是构造函数作用域）指向这个对象
- 给对象增加新属性，也就是执行构造函数的代码
- 返回这个新建的对象

可见使用`new`来调用函数，`this`参数就失效了，不会用传进来的`this`，但是参数还是有效的。所以需要判断。

构造函数尽量不要返回值。因为返回原始值不会生效，返回对象会导致`new`操作符没有作用。

`instance of`用来判断构造函数的原型是否在实例的原型链上。

```js
if (this instanceof F) {
  return new _this(...args, ...arguments);
}
```

如果是直接调用，使用`call`或者`apply`可以实现。

```js
_this.apply(context, args.concat(...arguments));
```

因为`bind`可以使用`bind(obj,1)(2)`的方式来调用，所以要对参数进行拼接。

```js
Function.prototype.myBind = function(context) {
  if (typeof this !== 'function') {
    throw new TypeError('Error');
  }
  const _this = this;
  const args = [...arguments].slice(1);
  return function F() {
    if (this instanceof F) {
      return new _this(...args, ...arguments);
    }
    return _this.apply(context, args.concat(...arguments));
  };
};
```

## 手写 new 操作符

```js
function myNew() {
  let obj = {};
  let arg = [].shift.call(arguments);
  obj.__proto__ = arg.prototype;
  let result = arg.apply(obj, arguments);
  return result instanceof Object ? result : obj;
}

console.log(myNew(Person, 'xxx', 'xx'));
```

## 手写 instance of

```js
function search(obj, proto) {
  let left = obj.__proto__;
  let right = proto.prototype;
  while (true) {
    if (left === null || left === undefined) {
      return false;
    } else if (right === left) {
      return true;
    }
    left = left.__proto__;
  }
}
```

## 关于对象中的 \_\_proto\_\_ 和 prototype

![](1111.jpg)

从该图来看，从左到右分为三个部分，第一部分是实例，第二部分是构造函数，第三部分是原型对象。

任何对象，都有一个`__proto__`指向它构造函数的原型对象。

`JS`中，方法和构造函数的唯一区别就是构造函数使用`new`调用，任何函数只要使用`new`调用，那么他就是构造函数。

所有方法的`__proto__`属性，也都指向自己构造函数的原型对象，也就是`Function`的原型。

任何方法（构造函数），都有一个`prototype`属性，指向它的原型对象，它的原型对象也有一个`constructor`，指回该方法。通过`Function.prototype.bind`方法构造出来的函数是个例外，它没有`prototype`属性。

通过构造函数`new`出来的实例，会将实例的`__proto__`指向自己的原型对象。也就是说，构造函数和实例唯一有关系的地方就是二者共享一个原型对象。只不过实例是通过`__proto__`指，构造函数是通过`prototype`指。

最后，所有原型对象的`__proto__`属性都指向最终的`Object.prototype`，`Object.prototype`的`__proto__`指向`null`。

至于`constructor`有什么用，现在肯定是没什么用了，`constructor`是构造函数原型上的属性，用来指向构造函数。`ES6`也已经增加了获取构造函数原型的方法`Reflect.getPrototypeOf(obj)`。早先可以通过下面的方式访问构造函数的原型。除了浏览器环境，其他环境没有`__proto__`这个东西。

![](222.jpg)
