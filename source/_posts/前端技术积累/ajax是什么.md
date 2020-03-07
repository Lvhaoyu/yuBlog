---
title: AJAX 是什么
tags:
  - web前端
categories:
  - 技术日志
  - JS基础
date: 2020-03-05 03:19:15
visible:
---

# AJAX 是什么

在很久以前没有 AJAX 的时候，浏览器发起一个请求，总会伴随着页面刷新，地址栏，`<a>`标签都会导致页面的刷新，只有请求特定的资源才不会造成页面的刷新，例如`<img>`标签，`<script>`标签等。直到 2004 年 Gmail 的出现，才将 AJAX 带入大家的视野。

简单来说，AJAX 就是使用`JS`向服务器发起一个请求并拿到服务器返回的内容的技术。特点在于请求多种类型资源时无需重新加载整个网页，可以只更新部分网页（局部刷新）。

## 如何发起一个 AJAX 请求

AJAX 最早的接口由微软在`IE5.0`实现，当时接口的名字叫做`Active("Microsoft.XMLHTTP")`，后来其他浏览器也相应跟进，提供了`XMLHttpRequest`接口，W3C 也将其纳入了标准。

如今在浏览器发起一个 AJAX 请求需要调用`XMLHttpRequest`接口来实现，下面来实现一个简单的 AJAX 请求。

### 使用 node 搭建一个服务器

```bash
.
├── AJAX.html
├── http.js
└── list.json
```

`list.json`为接口要返回的数据内容，`http.js`为搭建服务器代码，`ajax.html`为发起请求的前端页面。

Node 搭建服务器代码如下。

```js
var http = require('http');
var fs = require('fs');
const url = require('url');
var port = 3000;

http
  .createServer(function(req, res) {
    let path, str;
    if (req.method == 'GET') {
      let { pathname } = url.parse(req.url, true);
      path = pathname;
      complete();
    } else {
      res.end();
    }

    function complete() {
      if (path == '/list.json') {
        res.writeHead(200, {
          'Content-Type': 'text/plain;charset=utf-8'
        });
        fs.readFile('./list.json', (err, data) => {
          if (err) {
            console.log(err);
          } else {
            str = data.toString();
            res.end(str);
          }
        });
      } else {
        fs.readFile(`.${path}`, (err, data) => {
          if (err) {
            res.end('404');
          } else {
            res.end(data);
          }
        });
      }
    }
  })
  .listen(port, function() {
    console.log('server is on');
  });
```

这段代码实现了一个接收到前端相应请求后读取`list.json`文件并将内容返回给前端的接口。

### 编写前端代码

```html
<body>
  <button id="sendAjax">点击发送ajax</button>
  <div id="dataShow"></div>
  <script>
    document.getElementById('sendAjax').onclick = function() {
      var request = new XMLHttpRequest();
      request.open('GET', '/list.json', true);
      request.send();
      request.onreadystatechange = function() {
        if (this.readyState === 4 && this.status === 200) {
          document.getElementById('dataShow').innerHTML = request.responseText;
        }
      };
    };
  </script>
</body>
```

前端发起 AJAX 请求需要四步，第一步是通过`XMLHttpRequest()`构造函数初始化一个`XMLHttpRequest`对象。调用 AJAX 相关方法之前，必须通过该构造函数初始化。

```js
var request = new XMLHttpRequest();
```

第二步是使用`open`方法初始化一个请求，该方法可接收五个参数，一般只使用前三个参数。第一个参数指定要使用的`HTTP`方法，第二个参数是一个字符串，指明要发送请求的目标`URI`，第三个参数是一个布尔值，默认为`true`（异步请求），如果是异步请求，则`send`方法会在请求发送后立即返回；如果是同步请求，则`send`方法直到响应到达后才会返回。

```js
request.open('GET', '/list.json', true);
```

这里表示使用`GET`方法异步请求`list.json`文件内容。

第三步是使用`send`方法发送请求，该方法可以接收一个可选的参数作为请求主体（客户端传递给服务器的数据类型），如果是`GET`或`HEAD`请求应当将参数设置为`null`。

```js
request.send();
```

第四步就是监听请求成功后的状态变化，通过在`onreadystatechange`属性上绑定一个回调函数，在`readyState`属性的值发生变化时会触发`readystatechange`事件，调用对应的回调函数。

```js
request.onreadystatechange = function() {
  if (this.readyState === 4 && this.status === 200) {
    document.getElementById('dataShow').innerHTML = request.responseText;
  }
};
```

`readyState`表示一个`XMLHttpRequest`代理当前所处的状态。该状态有五个值，总是处于这五个值中的一个。

| 值  | 状态               | 描述                                                |
| --- | ------------------ | --------------------------------------------------- |
| `0` | `UNSENT`           | 代理被创建，但尚未调用`open()`方法。                |
| `1` | `OPENED`           | `open()` 方法已经被调用。                           |
| `2` | `HEADERS_RECEIVED` | `send()` 方法已经被调用，并且头部和状态已经可获得。 |
| `3` | `LOADING`          | 下载中， `responseText` 属性已经包含部分数据。      |
| `4` | `DONE`             | 下载操作已完成。                                    |

而`this.status`的值就是对应的`HTTP`状态码了，`200`表示请求成功。

### 请求成功之后的返回值

![](111.jpg)

请求成功之后会返回一个对象，里面存放着我们需要的各种数据和相应信息。

## AJAX 可以请求什么数据

最常使用的就是使用 AJAX 请求`JSON`数据，ajax 还可以请求如下几种数据。

- HTML 和 XHTML 内容
- CSS 内容
- JavaScript 内容
- XML 内容
- ...

## fetch 和 XMLHttpRequest 的关系

`fetch`是部署在全局对象上的一个`API`，是用来替代传统`XMLHttpRequest`发送 AJAX 请求的新方法。

`fetch`基于`Promise`设计，由于传统的`XMLHttpRequest`存在着配置和调用方式混乱，基于事件的异步模型也没有`ES6`新增的几个标准语义友好。`fetch`的引入就是为了解决`XHR`的短板。

`fetch`接收两个参数，第一个参数可以是一个请求资源的`URI`或一个`Request`对象（使用`new Request()`构造）。第二个可选参数就是发起请求可能需要的配置项（在 API 相关配置中提供）。

`fetch`的返回值是一个`Promise`对象，这意味着可以通过`then`方法来链式调用，也可以使用`async/await`来优化调用方式（注意捕获错误）。

```js
fetch(myRequest,myInit).then(function(response) {
  ...
});

async function fetchData() {
  try {
    let response = await fetch(url);
    let data = response.json();
    console.log(data);
  } catch (e) {
    console.log('Oops, error', e);
  }
}
```

## fetch 与 XMLHttpRequest 不同之处

- 当接收到一个代表错误的`HTTP`状态码时，从 `fetch()` 返回的`Promise`**不会被标记为 reject，** 即使响应的`HTTP`状态码是 404 或 500。相反，它会将`Promise`状态标记为`resolve`（但是会将 `resolve`的返回值的 `ok` 属性设置为`false`），仅当网络故障时或请求被阻止时，才会标记为`reject`。
- `fetch()` **不会接受跨域 cookies；**你也不能使用 `fetch()` 建立起跨域会话。其他网站的 `[Set-Cookie](https://wiki.developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie)` 头部字段将会被无视。
- `fetch` **不会发送 cookies**。除非你使用了`fetch(url, {credentials: 'include'})`。
