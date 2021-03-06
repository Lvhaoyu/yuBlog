---
title: HTTP 基本概念
tags:
  - web前端
  - 网络基础
categories:
  - 读书笔记
  - 图解HTTP
date: 2019-12-23 10:20:22
---

# 简单的 HTTP 协议

## HTTP 协议用于客户端和服务端的通信

`HTTP`协议是客户端`<-->`服务器模式的通信协议，请求访问的资源的一方为客户端，提供资源响应的一方为服务器。

同一台主机，可能在不同线路承担的角色不同，但是在一条通信线路，角色是确定的，`HTTP`协议可以明确区分它们。

## 通过请求和响应的交换达成协议

`HTTP`规定了请求从客户端发出，也就是由客户端开始建立通信，服务端收到请求后返回对应的请求信息，未收到请求不会响应。

### 请求报文

`HTTP`请求报文的请求行如下：

![](Jietu20200104-105614.jpg)

- `GET`表示请求服务器的类型，称为**方法**
- 方法后的字符串是请求资源所在的服务器上的路径，称为**请求 URI**
- 最后的`HTTP/1.1`是当前`HTTP`的版本号

翻译：_使用`HTTP 1.1`协议的 xx 方法来请求服务器上的 xxx 页面资源_。

请求报文整体组成如下：

![](Jietu20200104-122253.jpg)

### 响应报文

接收到请求的服务器，会将请求内容的处理结果以响应的形式返回。

响应报文整体组成如下：

![](Jietu20200104-122744.jpg)

响应头和响应体同样使用空行分隔。

可以请求报文和响应报文看出大体结构相同，但是和和请求报文相比，响应报文多了一个“响应状态码”，它以“清晰明确”的语言告诉客户端本次请求的处理结果。

## HTTP 是不保存状态的协议

`HTTP`是一种不保存状态，也就是所谓的“无状态”协议。协议不会对请求和响应的状态进行保存，就像是货车拉货，只负责将东西送到目的地，但不关心拉的货物是什么，也不关心之前拉没拉过相同的货物。对发送过的请求和接收到的响应都不做持久化处理。

`HTTP`每个新的请求就意味着会有对应新的响应产生，协议本身不保留之前的请求或者响应的信息，这样是为了方便处理大量的事务，伸缩性较高。

`cookie`就是为了给`HTTP`增加“状态”而使用的。

## 请求 URI 定位资源

`HTTP`通过`URI`定位互联网上的资源，放在请求报文的起始行中，可以写完整的请求`URI`来定位，也可以使用相对地址＋`HOST`属性来定位，如果不是访问特定资源，而是对服务器本身发出请求，可以用一个`*`来代替`URI`。

![](Jietu20200104-130655.jpg)

## HTTP 方法

### RESTful API

`RESTful API`是目前比较成熟的一套互联网应用程序的`API`设计理论。

> - 看`Url`就知道要什么
> - 看`http method`就知道干什么
> - 看`http status code`就知道结果如何

### GET

`Get`请求用来访问已经被`URI`识别的资源，指定的资源由服务器解析后返回响应内容。

RESTful 规范：从服务器取出资源（一项或多项）。

### POST

`POST`请求用来传输实体的主体，通常导致资源在服务器的状态变化或副作用。

RESTful 规范：在服务器新建一个资源。

`GET`和`POST`的区别？

幂等：指一次或者多次请求某一个资源应该具有同样的副作用，也就是保证服务资源多次请求状态一致，在`HTTP`中属于语义范畴，就是你最好遵守，但是规范没提。比如注册 10 个和 11 个帐号是不幂等的，对文章进行更改 10 次和 11 次是幂等的。因为前者是多了一个账号（资源），后者只是更新同一个资源。

- 查询天然幂等
- 删除也是幂等
- 更新某个值也是幂等
- 更新对其他数据造成影响的，非幂等（例如更新累加操作）
- 新增不是幂等

> 副作用指对服务器的资源进行改变，搜索是无副作用的，注册是有副作用的。

区别：

在规范的应用场景上说：

- `Get`多用于无副作用，幂等的场景，例如搜索关键字
- `Post`多用于副作用，不幂等的场景，例如注册

在技术上说：

- `Get`请求能缓存，`Post`不能
- `Post`相对`Get`安全一点点，因为`Get`请求都包含在`URL`里（当然你想写到`body`里也是可以的），且会被浏览器保存历史纪录。`Post`不会，但是在抓包的情况下都是一样的
- `URL`有长度限制，会影响`Get`请求，但是这个长度限制是浏览器规定的，不是`RFC`规定的
- `Post`支持更多的编码类型且不对数据类型限制

### PUT

`PUT`方法用于传输文件。要求在请求报文中包含文件内容，然后保存到指定的`URI`位置。

RESTful 规范：在服务器更新资源（客户端提供改变后的完整资源）。

### HEAD

`HEAD`方法与`GET`方法一样，但是不返回报文主体部分，用于确认`URI`的有效性及资源更新的日期等。

RESTful 规范：获取资源的元数据。

> 元数据(meta data)——“data about data” 关于数据的数据，一般是结构化数据（如存储在数据库里的数据，规定了字段的长度、类型等）。元数据是指从信息资源中抽取出来的用于说明其特征、内容的结构化的数据(如题名,版本、出版数据、相关说明,包括检索点等)，用于组织、描述、检索、保存、管理信息和知识资源。

### DELETE

`DELETE`方法用于删除文件，与`PUT`相反。

RESTful 规范：从服务器删除资源。

### TRACE

`TRACE`方法让`WEB`服务器将之前的请求通信返回给客户端的方法，发送请求时在`MAX_Forward`首部字段填入数值，每经过一个服务器减`1`，到`0`停止传输，最后收到请求的服务器返回响应报文（该报文中包含请求信息）。

该方法用来测试或者诊断错误，回显`Server`收到的请求，但是容易受到跨站追踪攻击，基本见不到。

### OPTIONS

`OPTIONS`方法用来查询针对请求`URI`指定的资源支持的方法。

RESTful 规范：获取信息，关于资源的哪些属性是客户端可以改变的。

### CONNECT

`CONNECT`要求在于代理服务器通信的时候建立隧道，实现用隧道协议进行`TCP`加密通信。

格式如下：

> CONNECT &nbsp;&nbsp;&nbsp;&nbsp;代理服务器名称:端口号 &nbsp;&nbsp;&nbsp;&nbsp;HTTP 版本

### PATCH

RESTful 规范独有：在服务器更新资源（客户端提供改变的属性），与同规范的`PUT`的区别在于，前者（`PUT`）需要客户端提供更新资源的全部信息，而后者只需要提供部分信息。

## 持久连接节省通信流量

早期`HTTP`传完一次资源就要断开连接，之后请求新的资源就要建立新的链接，每次都要三次握手就很耗性能，于是有了持久连接的出现。

### 持久连接

在`HTTP1.0`中，需要写入以下属性：`Connection:keep-alive`，该属性默认为`close`，但是在`HTTP1.1`中，默认打开。如果需要关闭需要显示的将属性设置为`close`。

这样就可以在一条连接上持续请求和响应，直到有一方主动断开连接，或者达到设置时间或最大次数。

#### 持久连接的断开

http/1.0：

响应头带`content-length`，`body`长度可知，客户端在接收`body`时，就可以依据这个长度来接收数据。接收完毕后，就表示这个请求完毕了。客户端主动调用`close`进入四次挥手。

响应头不带`content-length` ，`body`长度不可知，客户端一直接受数据，直到服务端主动断开。

http/1.1：

响应头带`content-length`，`body`长度可知则传输完毕客户端主动断开。

响应头带`Transfer-encoding：chunked`，`body`会被分成多个块，每块的开始会标识出当前块的长度，`body`就不需要通过`content-length`来指定了。但依然可以知道`body`的长度，由客户端主动断开。

响应头不带`Transfer-encoding：chunked`且不带`content-length`，客户端接收数据，直到服务端主动断开连接。

### 管线化（pipelineing）

`HTTP`管线化是将多个`HTTP`请求（request）整批提交的技术，而在传送过程中不需先等待服务端的回应。管线化机制须通过永久连接（persistent connection）完成，仅`HTTP/1.1`支持此技术（`HTTP/1.0`不支持），并且只有`GET`和`HEAD`请求可以进行管线化，而`POST`则有所限制。此外，初次创建连接时也不应启动管线机制，因为对方（服务器）不一定支持`HTTP/1.1`版本的协议。

浏览器将`HTTP`请求并行提交可大幅缩短页面的加载时间，特别是在传输延迟较高的情况下（如卫星连接），无需等待响应就可以发送下一个请求。此技术之关键在于多个`HTTP`的请求消息可以同时塞入一个`TCP`分组中，所以只提交一个分组即可同时发出多个请求，借此可减少网络上多余的分组并降低线路负载。

#### 什么请求可以管线化

只有幂等的请求可以管线化。

> Non-idempotent(非幂等) methods like POST should not be pipelined. Sequences of GET and HEAD requests can be always pipelined. A sequence of other idempotent requests like GET, HEAD, PUT and DELETE can be pipelined or not depending on whether requests in the sequence depend on the effect of others.

## 使用 cookie 进行状态管理

由于`HTTP`是无状态协议，因为这样可以保持协议绝对简单，对服务器资源的消耗较小。但是当需要在跳转页面的时候保持登录状态时就无法实现，这时就需要使用`cookie`。

`cookie`一般由服务器生成，可以设置过期时间，服务器通过在响应报文中增加`Set-Cookie`的字段通知客户端保存信息，下次请求会将`cookie`放在请求头中发送到服务器。

`cookie`存储大小只有`4k`，每次都会携带在请求头中，对请求性能有一定的影响。

以下字段可以控制`cookie`：

![](Jietu20200104-174754.jpg)

### Session

`Session`是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中。

`Cookie`也是实现`Session`的一种方式，第一次创建`Session`的时候，服务端会在`HTTP`协议中告诉客户端，需要在`Cookie`里面记录一个`Session ID`（其实就是在`Set-Cookie`中数据指定好了，客户端不知道里面的数据是啥），以后每次请求把这个会话 ID 发送到服务器。若客户端把`cookie`禁用了，就使用`URL`重写技术，携带`Session ID`。每次客户端发请求服务端都会检查有没有对应的`Session ID`，如果没有，就认为是第一次创建`Session`。

注意：`Session`是一个抽象的概念，但是`cookie`是真实存在的，使用`cookie`和`HTTP`是对`Session`的具体实现。

> 开发者为了实现中断和继续等操作，将`user agent`和`server`之间一对一的交互，抽象为“会话”，进而衍生出“会话状态”，也就是`session`的概念。
