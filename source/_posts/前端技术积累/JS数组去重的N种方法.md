---
title: JS数组去重的N种方法
tags:
  - web前端
categories:
  - 技术日志
  - JS基础
date: 2020-03-07 09:24:19
visible:
---

# JS 数组去重的 N 种方法

## 使用 Array.from() 配合 Set 去重

```js
function arrayUnique(arr) {
  return Array.from(new Set([...arr]));
}
```

也可使用简单的方法。

```js
[...new Set(arr)];
```

## 使用 reduce 去重

```js
function arrayUnique1(arr) {
  return arr.reduce(
    (pre, cur) => (pre.includes(cur) ? pre : [...pre, cur]),
    []
  );
}
```

## 使用双重循环去重

```js
function arrayUnique2(arr) {
  for (let i = 0; i < arr.length; i++) {
    for (let j = i + 1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {
        arr.splice(j, 1);
        j--;
      }
    }
  }
  return arr;
}
```

## 使用新数组去重

```js
function arrayUnique3(arr) {
  let arr1 = [];
  for (let i = 0; i < arr.length; i++) {
    if (arr1.indexOf(arr[i]) < 0) {
      arr1.push(arr[i]);
    }
  }
  return arr1;
}
```

## 排序后使用循环去重

```js
function arrayUnique4(arr) {
  arr = arr.sort();
  let array = [arr[0]];
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] !== arr[i - 1]) {
      array.push(arr[i]);
    }
  }
  return array;
}
```

## 使用 includes 去重

```js
function arrayUnique5(arr) {
  let array = [];
  for (let i = 0; i < arr.length; i++) {
    if (!array.includes(arr[i])) {
      array.push(arr[i]);
    }
  }
  return array;
}
```

## 使用 filter 去重

```js
function arrayUnique6(array) {
  return array.filter((v, i, arr) => {
    return i === arr.lastIndexOf(v);
  });
}
```

## 使用 Map 去重

因为`Map`的遍历循序就是插入顺序，所以也可以很方便的找出第一个重复或不重复的数字。

```js
function arrayUnique7(arr) {
  let map = new Map();
  let array = [];
  for (let i = 0; i < arr.length; i++) {
    if (map.has(arr[i])) {
      map.set(arr[i], true);
    } else {
      map.set(arr[i], false);
      array.push(arr[i]);
    }
  }
  return array;
}
```
