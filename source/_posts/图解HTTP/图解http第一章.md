---
title: HTTP 的诞生
tags:
  - web前端
  - 网络基础
categories:
  - 读书笔记
  - 图解HTTP
date: 2019-09-23 19:57:43
---

## 使用 HTTP 访问 WEB

> `HTTP`是基于`TCP/IP`协议的应用层协议。它不涉及数据包`(packet)`传输，主要规定了客户端和服务器之间的通信格式，默认使用`80`端口。

简单来说，`HTTP`就是客户端用来获取服务端资源的协议，具体表现在我们在浏览器中输入一个`URL`地址，该信息会被送往某处，然后从某处获得回复，通过浏览器解析渲染后将回复展示给用户，这个过程中使用的协议就是`HTTP`协议。

`HTTP`全名为（HyperText Transfer Protocol），超文本传输协议，这里可能会有疑问，按照`HTTP`性质来说，翻译成超文本转移协议更好，因为`HTTP`只负责在应用层转移，不会关心具体的运输。但是由于前一种叫法已经约定俗成，所以沿用超文本传输协议。

这里可能会想到`HTML`被称为超文本标记语言，顾名思义，超文本传输协议最初产生就是用来传输超文本标记语言的协议。

而现在的`WEB`系统，就是基于`HTTP`和`HTML`的，利用浏览器进行访问的网站。（图形信息系统）

## HTTP 的诞生

### HTTP 的由来

`HTTP`最早是为了探讨学术，最初的基本理念是借助多文档之间相互关联的超文本，连成可以相互借阅的`WWW`(万维网)。

超文本就是文档里面有很多超链接，这些链接将很多文档相互组合起来。

现在有三项`WWW`构建技术，分别是`HTML`，`HTTP`，`URL`。

而`WWW`以前被用作浏览超文本的客户端应用程序（`WEB`浏览器）的名字，现在表示以上三项的集合，也简称为`WEB`。

### WEB 发展历史

1990.11：第一台`WEB`服务器和`WEB`浏览器出现=>`HTTP/0.9`
1993.1：马赛克通信公司研发 Mosaic 浏览器 => `Html/1.0`
1994.4：马赛克公司改名为网景

之后网景开始与`IE`竞争，2000 年，网景眼看打不过了，将浏览器开源，这就是火狐前身，负责开源的组织叫`Mozilla`，也就是`MDN`的发起者。

### HTTP 的历史

`HTTP/0.9`：1990，泛指`1.0`以前所有的版本
`HTTP/1.0`：1996.5，初期标准，但是使用还很广泛。
`HTTP/1.1`：1997.1， 主流的`HTTP`标准

## 网络基础 TCP/IP

网络是在`TCP/IP`协议族的基础上进行运作的，`HTTP`属于其内部的一个子集。

### TCP/IP 协议族

计算机与网络设备进行通信，双方必须遵循约定好的方法，这种方法就叫协议。

`TCP/IP`协议族在这里泛指与互联网相关联的协议集合。

### TCP/IP 分层管理

`TCP/IP`协议族分为四层，如下：

![](fourfloor.jpg)

分层的好处就是如果协议发生改变，只需要改变发生变化的那一层，各层只需要关心自己的事情，设计相对来说也会变得简单。

- 应用层：由系统调用向下通信（`FTP`，`HTTP`，`DNS`）
- 传输层：负责计算机到计算机之间的数据传输（`TCP`，`UDP`）
- 网络层：负责规划数据包路线怎么走（`IP`）
- 链路层：处理网络的硬件部分

### 传输过程

网络通信时，发送端从上往下走，接收层从下往上走。

![](16a96519a1c40126.jpg)

首先在应用层发送一个`HTTP`请求，接着，在传输层把应用层接受到的`HTTP`报文进行切割，在各个报文段打上标记序号以及端口号向下转发，在网络层增加作为通信目的地的`MAC`地址，之后将信息传给链路层，发送到接收方，接收方收到后，一层一层解析，最后到达应用层，客户端才算是接收到了`HTTP`请求。

由图中可以看出，发送时每经过一层就会打上该层的首部信息，接收时每经过一层就会消除对应的首部信息。这就是数据封装与解封装的过程。

## IP、TCP、DNS

### 负责传输的 IP 协议

`IP`全名网际协议，位于网络层，`IP`协议的作用就是将各种数据包发送给对方，为了保证传输的准确性，需要满足各种条件，最主要的就是`IP`地址和`MAC`地址。

- `IP`地址：指明节点被分配到的位置
- `MAC`地址：网卡所属的固定地址

二者可以进行配对，`IP`地址可以变换，但是`MAC`地址一般不会更改。

#### 使用 ARP 协议凭借 MAC 地址进行通信

`IP`的通信依赖`MAC`地址，但是通信的双方绝大部分都是需要多台计算机和网络设备中转才能到达对方。中转时，利用下一站的`MAC`地址搜索对应`MAC`地址号的的中转目标。

> 包`(Packet)`是`TCP/IP`协议通信传输中的数据单位，一般也称“数据包”。 `TCP/IP`协议是工作在`OSI`模型第三层(网络层)、第四层(传输层)上的，帧工作在第二层(数据链路层)。 上一层的内容由下一层的内容来传输，所以在局域网中，“包”是包含在“帧”里的。

`ARP`协议就是一种用来解析地址的协议，根据通信双方的`IP`地址就可以反查出对应的`MAC`地址。

#### 无人可以掌握互联网中的传输状况

在到达目标前，计算机和路由器只能大致估算出粗略的传输路线，这种机制被称为路由选择。因为路线的判断不是一开始规划好的，而是到对应的中转设备后再分配对应的路线。

![](ip.jpeg)

### 确保可靠的 TCP 协议

`TCP`位于传输层，提供可靠的字节流服务。

- 字节流服务：为了方便传输，将大块数据分割成以报文段为单位的数据包进行管理
- 可靠：能够将数据准确可靠的传给对方（就是能确认对方收没收到）

#### 三次握手

为了保证传输数据的可靠性，需要三次握手才能确认双方的接收与发送能力是否正常

三次握手是在浏览器得到`DNS`服务器返回的`IP`地址之后开始进行的。

首先认识一下`TCP`的头部：

![](1631be45b084e4bc.jpg)

- SYN：表示建立连接，当`SYN=1`，`ACK=0`时，表示当前报文段是一个连接请求报文。当`SYN=1`，`ACK=1`时，表示当前报文段是一个同意建立连接的应答报文
- ACK：表示确认号字段状态，建立连接后必须为`1`，请求连接时可以为`0`
- FIN：`FIN=1`表示当前报文段为释放连接请求报文
- Acknowledgement Number：这个序号表示数据接收端期望接收的下一个字节的编号是多少，同时也表示上一个序号的数据已经收到（简称`ackNumber`）
- Sequence number：这个序号保证了`TCP`传输的报文都是有序的，对端可以通过序号顺序的拼接报文（简称`Seq`，第一次为`ISN`），该序列号动态生成

![](1631bf1e79b3cd42.jpg)

第一次握手：客户端发送请求报文，该报文中`SYN=1`，`Seq`为初始序列号（`ISN`），进入到`SYN_SEND`状态。

- `SYN`攻击：服务器端的资源分配是在二次握手时分配的，而客户端的资源是在完成三次握手时分配的，所以服务器容易受到`SYN`洪泛攻击。`SYN`攻击就是`Client`在短时间内伪造大量不存在的`IP`地址，并向`Server`不断地发送`SYN`包，`Server`则回复确认包，并等待`Client`确认，由于源地址不存在，因此`Server`需要不断重发直至超时，这些伪造的`SYN`包将长时间占用未连接队列，导致正常的`SYN`请求因为队列满而被丢弃,检测 `SYN` 攻击非常的方便，当你在服务器上看到大量的半连接状态时，特别是源`IP`地址是随机的，基本上可以断定这是一次`SYN`攻击。在`Linux/Unix`上可以使用系统自带的`netstats`命令来检测`SYN`攻击

第二次握手：服务器收到请求报文后，会以自己的报文作为应答，也指定了自己的`Seq`（`ISN`初始序列号），同时`ackNumber`为第一次握手的`Seq+1`，进入到`SYN_RCVD`状态。

- 半连接队列：服务器第一次收到客户端的`SYN`之后，就会处于`SYN_RCVD`状态，此时双方还没有完全建立其连接，服务器会把此种状态下请求连接放在一个队列里，我们把这种队列称之为半连接队列。当然还有一个全连接队列，就是已经完成三次握手，建立起连接的就会放在全连接队列中。如果队列满了就有可能会出现丢包现象。服务器发送完`SYN-ACK`包，如果未收到客户确认包，服务器进行首次重传，等待一段时间仍未收到客户确认包，进行第二次重传。如果重传次数超过系统规定的最大重传次数，系统将该连接信息从半连接队列中删除。注意，每次重传等待的时间不一定相同，一般会是指数增长，例如间隔时间为 1s，2s，4s，8s…

第三次握手：最后，客户端收到回应后，会再发送一个`ACK`报文，`Seq`为第一次的`ISN+1`，`ackNumber`为服务器发来的`ISN+1`，最后二者都进入`ESTABLISH`状态。

- 此次握手可以携带数据

#### 四次挥手

![](1631fb807f2c6c1b.jpg)

第一次挥手：若客户端认为数据发送完成，则它需要向服务端发送连接释放请求

第二次挥手：服务端收到连接释放请求后，会告诉应用层要释放`TCP`链接。然后会发送`ACK`包，并进入`CLOSE_WAIT`状态，此时表明客户端到服务器的连接已经释放，不再接收客户端发的数据了。但是因为`TCP`连接是双向的，所以服务端仍旧可以发送数据给客户端。

第三次挥手：服务器如果此时还有没发完的数据会继续发送，完毕后会向客户端发送连接释放请求，然后服务器便进入`LAST-ACK`状态。

第四次挥手：客户端收到`FIN`之后，一样发送一个`ACK`报文作为应答，且把服务端的序列号值`+1`作为自己`ACK`报文的序列号值，此时客户端处于`TIME_WAIT`状态。需要过一阵子以确保服务端收到自己的 `ACK`报文之后才会进入`CLOSED`状态，服务端收到`ACK`报文之后，就处于关闭连接了，处于`CLOSED`状态。

> `TIME_WAIT`状态就是用来重发可能丢失的`ACK`报文。

四次挥手的主要原因是因为当服务端收到客户端的`SYN`连接请求报文后，可以直接发送`SYN+ACK`报文。其中`ACK`报文是用来应答的，`SYN`报文是用来同步的。但是关闭连接时，当服务端收到`FIN`报文时，很可能并不会立即关闭`SOCKET`，所以只能先回复一个`ACK`报文，告诉客户端，“你发的`FIN`报文我收到了”。只有等到我服务端所有的报文都发送完了，我才能发送`FIN`报文，因此不能一起发送。故需要四次挥手。根源上是因为`TCP`提供了连接的一端在结束它的发送后还能接收来自另一端数据的能力（半关闭`half-close`）。

> `Socket`是对`TCP/IP`协议的封装，`Socket`只是个接口不是协议，通过`Socket`我们才能使用`TCP/IP`协议，除了`TCP`，也可以使用`UDP`协议来传递数据。

### DNS 服务

`DNS`服务是位于应用层的提供域名到`IP`地址的解析服务。

具体查找过程如下：输入网址 `-->` 本地`HOST`文件 `-->` 本地`DNS`解析器缓存 `-->` 首选本地`DNS`服务器（运营商） `-->` 设置转发了吗？ `-->` 是 `-->` 根据设置的转发服务器向上找 || 否 `-->` `13`台根服务器

## URI 与 URL

`URI`：统一资源标识符，用来表示资源的定位标识符，使用紧凑的字符串来表示抽象的或者物理的资源。

> Uniform：规定统一的格式可方便处理多种不同类型的资源
> Resource：可标识的任何东西
> Identifier：标识符

`URN`：表示资源的名字，身份，是`URI`的一部分。
`URL`：表示资源的地址，提供找到`URN`的地址。

`URI`包括了`URN`和`URL`，是二者的超集。

绝对`URI`格式如下：

![](URI.png)

## 总结

- 介绍了`HTTP`发展历史
- 介绍了`TCP/IP`协议族
- 介绍了`HTTP`以及相关技术的概念
