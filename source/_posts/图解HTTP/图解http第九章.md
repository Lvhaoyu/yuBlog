---
title: 图解http第九章
tags:
  - web前端
  - 网络基础
categories:
  - 读书笔记
  - 图解HTTP
date: 2020-01-23 09:30:59
visible:
---

# 基于 HTTP 的功能追加协议

## HTTP 的不足

`HTTP`虽然既简单又快捷，但是随着时代的发展。`HTTP`的局限性逐渐显现出来，由于其建立在`TCP/IP`协议基础之上的应用层，所以`HTTP`的瓶颈和优化技巧基本都要要在`TCP/IP`上做文章。

影响`HTTP`请求的因素：

- 带宽
- 延迟

带宽如今已经不是问题，不论是手机端还是`PC`端，网速都已经很快了。主要的因素在于延迟，延迟可以分为以下几个部分：

- 浏览器的阻塞
  - 一条连接上只可发送一个请求，浏览器有最大连接限制。
  - `DNS`查询时间，可以看通过缓存优化
  - 新的请求建立连接花费时间
- 请求只能从客户端开始。客户端不可以接收除响应以外的指令。
- 请求 / 响应首部未经压缩就发送。首部信息越多延迟越大。
- 发送冗长的首部。每次互相发送相同的首部造成的浪费较多。
- 可任意选择数据压缩格式。非强制压缩发送。

## 基于 HTTP 的改进

### AJAX 的解决方法（轮询）

> Ajax（Asynchronous JavaScript and XML， 异 步 JavaScript 与 XML 技术）是一种有效利用 JavaScript 和 DOM（Document Object Model，文档对象模型）的操作，以达到局部 Web 页面替换加载的异步通信手段。和以前的同步通信相比，由于它只更新一部分页面，响应中传输的数据量会因此而减少，这一优点显而易见。

`Ajax`的核心技术是名为`XMLHttpRequest`的`API`，通过`JavaScript`脚本语言的调用就能和服务器进行`HTTP`通信。借由这种手段，就能从已加载完毕的`Web`页面上发起请求，只更新局部页面。

而利用`Ajax`实时地从服务器获取内容，有可能会导致大量请求产生。另外，`Ajax`仍未解决`HTTP`协议本身存在的问题。

### Comet 的解决方法（长轮询）

一旦服务器端有内容更新了，`Comet` 不会让请求等待，而是直接给客户端返回响应。这是一种通过延迟应答，模拟实现服务器端向客户端推送（Server Push）的功能。

通常，服务器端接收到请求，在处理完毕后就会立即返回响应，但为了实现推送功能，`Comet` 会先将响应置于挂起状态，当服务器端有内容更新时，再返回该响应。因此，服务器端一旦有更新，就可以立即反馈给客户端。

内容上虽然可以做到实时更新，但为了保留响应，一次连接的持续时间也变长了。期间，为了维持连接会消耗更多的资源。另外，`Comet` 也仍未解决`HTTP`协议本身存在的问题。

## WebSocket 与 HTTP

`WebSocket`和`HTTP`有交集，但不是完全包含`HTTP`，可以理解为一个新的协议，但是需要基于`HTTP`来实现。为了兼容现有`HTTP`的一些规范，才有了一些交集。

`WebSocket`是一个持久化的协议。即`Web`浏览器与`Web`服务器之间全双工通信标准。由于是建立在`HTTP`基础上的协议，因此连接的发起方仍是客户端，而一旦确立`WebSocket`通信连接，不论服务器还是客户端，任意一方都可直接向对方发送报文。

### WebSocket 握手

客户端请求`WebSocket`报文如下：

```http
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket // 核心，告诉服务器使用websocket协议
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ== // base64加密，验证服务器是不是websocket助理
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat // 同一URL下不同服务使用的协议
Sec-WebSocket-Version: 13 // 协议版本
```

> Sec-WebSocket-Key 字段内记录着握手过程中必不可少的键值。Sec-WebSocket-Protocol 字段内记录使用的子协议。子协议按 WebSocket 协议标准在连接分开使用时，定义那些连接的名称。

响应报文如下：

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat // 最终使用的协议
```

> Sec-WebSocket-Accept 的字段值是由握手请求中的 Sec-WebSocket-Key 的字段值生成的。

服务器发送响应报文说明自己已经接收到建立起`WebSocket`连接的请求。`Sec-WebSocket-Accept`相当于身份证，证明自己的身份。成功握手确立`WebSocket`连接之后，通信时不再使用`HTTP`的数据帧，而采用`WebSocket`独立的数据帧。

### WebSocket 协议

由于`HTTP`请求是“被动的”，只能由客户端发送请求建立连接，客户端不能处理除了响应以外的报文。于是有了`AJAX`轮询（客户端每隔一段时间发送一段请求）和长轮询（阻塞模型，服务器没消息就不回应，有消息才返回给客户端）等方式进行即时通讯。

这两种都是不断发起连接，等待响应。`AJAX`轮询要求客户端每隔一段时间就发送请求，服务端收到后即时响应。对客户端和服务器资源消耗很大，需要更快的处理速度。长轮询虽然减少了不必要的`HTTP`请求次数，但是要求服务器支持高并发。

这时为了解决上述的问题，引入了`WebSocket`协议，只需要发送一次`HTTP`请求建立`WebSocket`连接。一旦确立`WebSocket`通信连接，不论服务器还是客户端，任意一方都可直接向对方发送报文。

`WebSocket`协议主要特点：

- 推送功能：支持由服务器向客户端推送数据的推送功能。这样，服务器可直接发送数据，而不必等待客户端的请求。

- 减少通信量：只要建立起`WebSocket`连接，就希望一直保持连接状态。和`HTTP`相比，不但每次连接时的总开销减少，而且由于`WebSocket`的首部信息很小，通信量也相应减少了。可以说整个通讯过程是建立在一次连接（状态）中，也就避免了`HTTP`的无状态性，服务端会一直知道你的信息，直到你关闭请求，这样就避免了服务器要反复解析`HTTP`协议。

- 所用的程序是要经过两层代理的，即`HTTP`协议在`Nginx`等服务器的解析下，然后再传送给相应的`Handler`（PHP 等）来处理。一般来说`Nginx`是很快的，但是`Handler`速度不够。`WebSocket`解决的就是客户直接与`Nginx`建立持久连接，有信息的时候`Handler`直接通知`Nginx`，再统一转发给客户。这样就解决了速度慢的问题。

整个流程如下：

![](00171.jpeg)

## SPDY 协议

`SPDY`是对`HTTP`的增强，并不是要替代`HTTP`协议，没有完全改写`HTTP`协，而是在`TCP/IP`的应用层与运输层之间通过新加会话层的形式运作。同时，考虑到安全性问题，`SPDY`规定通信中使用`SSL`。

`SODY`以会话层的形式加入，控制对数据的流动，但还是采用`HTTP`建立通信连接。因此，可照常使用`HTTP`的`GET`和`POST`等方法、`Cookie`以及`HTTP`报文等。

![](00170.jpeg)

### SPDY 解决的问题

新协议的出现必定是为了解决老协议的问题，`SPDY`主要针对`HTTP`以下几个问题进行改良：

- 单路连接，请求低效
- HTTP 只允许客户端主动发起请求
- HTTP 头部冗余

#### 多路复用 请求优化

通过单一的`TCP`连接，可以无限制的处理多个`HTTP`请求。所有请求的响应都在一条`TCP`连接上完成，因此`TCP`的处理效率得到提高。`SPDY`不仅可以无限制地并发处理请求，还可以给请求逐个分配优先级顺序。这样在发送多个请求时，因带宽低而导致响应变慢的问题可以得到改善。

#### 支持服务器推送技术

支持服务器主动向客户端推送数据的功能。服务器可直接发送数据，而不必等待客户端的请求。该技术和`WebSocket`的不同之处在于`SPDY`多了服务器提示功能，服务器可以主动提示客户端请求所需的资源。由于在客户端发现资源之前就可以获知资源的存在，因此在资源已缓存等情况下，可以避免发送不必要的请求。

#### 压缩 HTTP 头部

压缩`HTTP`请求和响应的首部。这样一来，通信产生的数据包数量和发送的字节数就更少了。

### SPDY 不足之处

使用`SPDY`需要客户端和服务器都要做改动，这是一个很大的问题。此外`SPDY`基本上只是将单个域名（ IP 地址）的通信多路复用，当一个`Web`网站上使用多个域名下的资源，改善效果就会受到限制。

因为以上种种原因，还没推广，就被`HTTP/2.0`取代了。

## HTTP/2.0

`HTTP/2.0`相当于`SPDY`的升级版，有很多相似之处。

### HTTP/2.0 性能增强的核心：二进制分帧

`HTTP/2.0`最大的特点就是没有改变`HTTP/1.1`的核心概念，却突破了其性能限制。新增了二进制分帧层，实现了低延迟和高吞吐量的突破。

![](116.png)

`HTTP/2.0`会将请求报文消息分割成更小的帧并进行二进制编码。`HTTP/2.0`请求和响应都在一个连接上进行，可以进行双向通信，并且报文可以乱序发送。

### HTTP/2.0 首部压缩

![](117.png)

二进制分帧同样带来了一个问题，报文都被切割，那么报文头是不是每个帧都要带一份呢？很明显不是的。

`HTTP/2.0`使用首部表来跟踪和存储头部的键值对，只发送变化的首部。并且首部表不断更新，将新增的首部加进去。首部表是通信双方各缓存一份，避免了重复的传输。

### HTTP/2.0 多路复用

`HTTP/2.0`将通信单位缩小为一个一个的帧，性能的关键在于低延迟。所以将所有的请求都在一条连接上发送，就会最大的提升带宽的优势。

![](118.png)

单链接多资源的优势：

- 减少服务器的压力，内存占用减少，连接吞吐量变大
- 由于`TCP`连接减少而使网络拥塞情况得以改观
- 慢启动时间减少，拥塞和丢包恢复速度快

> 慢启动（Slow Start），是传输控制协议（TCP）使用的一种阻塞控制机制。 慢启动也叫做指数增长期。 慢启动是指每次 TCP 接收窗口收到确认时都会增长。 增加的大小就是已确认段的数目。

![](119.png)

除了多路复用之外，`HTTP/2.0`也支持并行双向字节流的请求和响应。`HTTP/2.0`会将消息拆分为互不依赖的帧乱序发送，另一端重新组装。

这样带来的好处有以下几点：

- 并行交错地发送请求，请求之间互不影响
- 并行交错地发送响应，响应之间互不干扰
- 只是用一个连接即可并行发送多个请求和响应
- 消除不必要的延迟，减少页面加载的时间

### HTTP/2.0 请求优先级

- 高优先级的流都应该优先发送
- 优先级不是绝对的，否则可能会导致阻塞
- 为了提高效率，不同优先级的混合也是必须的

### HTTP/2.0 服务器推送

![](120.png)

服务器可以对一个客户端的请求发送多个响应，服务器在客户端没有请求的情况下推送一些资源，让传输更加高效。

## 扩展 HTTP/1.1 的 WebDAV 协议

`WebDAV`协议是基于`Web`的分布式编写和版本控制（WebDAV）是超文本传输协议（HTTP）的扩展，有利于用户间协同编辑和管理存储在万维网服务器文档。

![](00172.jpeg)

除了创建、删除文件等基本功能，它还具备文件创建者管理、文件编辑过程中禁止其他用户内容覆盖的加锁功能，以及对文件内容修改的版本控制功能。因为该协议是实时操作，对流量，实时读写要求很高。

为了进行文件管理，`WebDAV`增加了几个方法和状态码：

- PROPFIND ：获取属性

- PROPPATCH ：修改属性

- MKCOL ：创建集合

- COPY ：复制资源及属性

- MOVE ：移动资源

- LOCK ：资源加锁

- UNLOCK ：资源解锁

- 102 Processing ：可正常处理请求，但目前是处理中状态

- 102 Processing ：可正常处理请求，但目前是处理中状态

- 207 Multi-Status ：存在多种状态

- 422 Unprocessible Entity ：格式正确，内容有误

- 423 Locked ：资源已被加锁

- 424 Failed Dependency ：处理与某请求关联的请求失败，因此不再维持依赖关系

- 507 Insufficient Storage ：保存空间不足

由于已经有`FTP`了，该协议还没有被广泛使用，一般都是`NAS`和一些需要在线编辑文档的网站在用。

下面是使用`WebDav`的实例：

```XML
// 请求报文
PROPFIND /file HTTP/1.1
Host: www.example.com
Content-Type: application/xml; charset="utf-8"
Content-Length: 219
　
<?xml version="1.0" encoding="utf-8" ?>
<D:propfind xmlns:D="DAV:"> // 命名空间元素声明，彻底检查命名空间查找属性
  <D:prop xmlns:R="http://ns.example.com/boxschema/">
    <R:bigbox/>
    <R:author/>
    <R:DingALing/>
    <R:Random/>
  </D:prop>
</D:propfind>

// 响应报文
HTTP/1.1 207 Multi-Status
Content-Type: application/xml; charset="utf-8"
Content-Length: 831
　
<?xml version="1.0" encoding="utf-8" ?>
<D:multistatus xmlns:D="DAV:">
  <D:response xmlns:R="http://ns.example.com/boxschema/">
    <D:href>http://www.example.com/file</D:href>
    <D:propstat>
      <D:prop>
        <R:bigbox>
        <R:BoxType>Box type A</R:BoxType>
        </R:bigbox>
        <R:author>
        <R:Name>J.J. Johnson</R:Name>
        </R:author>
      </D:prop>
      <D:status>HTTP/1.1 200 OK</D:status>
    </D:propstat>
    <D:propstat>
      <D:prop><R:DingALing/><R:Random/></D:prop>
      <D:status>HTTP/1.1 403 Forbidden</D:status>
      <D:responsedescription> The user does not have access to the DingALing property.
      </D:responsedescription>
    </D:propstat>
  </D:response>
  <D:responsedescription> There has been an access violation error.
  </D:responsedescription>
</D:multistatus>
```

## QUIC & HTTP/3.0

`HTTP/2.0`还没普及，现在谈论`HTTP/3.0`还为之尚早。谷歌早在 2013 年就提出了`QUIC`（快速 UDP 互联网连接）协议，该协议是用来解决网络层的`TCP`传输带来的种种问题。

### HTTP/2.0 的问题

- 多路复用会导致队头阻塞，丢包的情况下影响其他连接
- 建立连接的握手延迟大

### QUIC 的特性

`QUIC`基于`UDP`，并进行了改革。

- 缓存会话上下文，实现`0RTT`建立连接/加密连接
- 没有队头阻塞的多路复用，基于`UDP`，多个包之间没什么依赖，不像`TCP`那样丢一个包就会重传，导致后面的包阻塞。
- 移动端网络多变，使用`id`实现一个连接，只要`id`不变，就能迅速建立连接。
- 前项纠错功能，每个包除了自己的部分还有其他数据包的部分内容，丢包可以使用这些多余的包进行重新组装，防止重传。具体实现是会有一个校验包，只有丢了一个非校验包，才能根据校验包重新组装（也只支持丢一个非校验包重新组装）。
