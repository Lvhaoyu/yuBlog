---
title: 什么是 HTTPS
tags:
  - web前端
  - 网络基础
categories:
  - 读书笔记
  - 图解HTTP
date: 2020-01-16 10:48:36
visible:
---

# 第七章：确保 WEB 安全的 HTTPS

由于`HTTP`是一个非常简单的协议，因此存在着很多不可避免的安全问题。`HTTPS`的出现正是对`HTTP`协议的扩充，有效的解决了`HTTP`中存在的的安全问题。

## HTTP 的缺点

`HTTP`的不足主要有以下几点：

- 通信使用明文（不加密），内容可能会被窃听

- 不验证通信方的身份，因此有可能遭遇伪装

- 无法证明报文的完整性，所以有可能已遭篡改

### 通信使用明文可能被窃听

`HTTP`没有加密的功能，发送报文都是以明文的方式进行发送。之前说过`HTTP`是基于`TCP/IP`协议的。按照`TCP/IP`协议族的工作机制，通信内容在所有的通信线路上都有可能遭到窥视。

#### TCP/IP 是可能被窃听的网络

互联网是由全世界所有连接到一起的计算机组成，当你的计算机连上网络，它注定已经不是你的私有物，而是暴露在全世界的电脑之中。

窃听和报文是否加密无关，加密后的报文也可以被窃听。通过抓包就可以窃听，如果报文是明文，数据就会泄露。但如果是加密后的报文，就算获得报文，也无法得知报文的信息。

#### 加密处理和方式被窃听

为了对抗窃听，最普及的就是使用加密技术。加密技术无非就对两种对象进行加密，第一种是报文，第二种是传输报文的通道。

##### 通信的加密

`HTTP`协议中没有加密机制，但可以通过和`SSL`（Secure Socket Layer，安全套接层）或`TLS`（Transport Layer Security，安全层传输协议）的组合使用，加密`HTTP`的通信内容。

用`SSL`建立安全通信线路之后，就可以在这条线路上进行`HTTP`通信了。与`SSL`组合使用的`HTTP`被称为`HTTPS`（HTTP Secure，超文本传输安全协议）或`HTTP over SSL`。

##### 内容的加密

内容的加密指的是对`HTTP`协议传输的内容本身进行加密。即把`HTTP`报文里所含的内容进行加密处理。

在这种情况下，客户端需要对`HTTP`报文进行加密处理后再发送请求。加密机制只对`HTTP`报文的主体进行加密，报文首部不进行加密。

### 不验证通信方的身份就可能遭遇伪装

`HTTP`协议中的请求和响应不会对通信方进行确认。也就是说存在“服务器是否就是发送请求中`URI`真正指定的主机，返回的响应是否真的返回到实际提出请求的客户端”等类似问题。

#### 任何人都可以发起请求

在`HTTP`协议通信，不存在对通信方进行确认的步骤，在发送端的`IP`地址和端口号没有被`Web`服务器限制访问的情况下，任何人都可以发起请求。服务器只管接收请求，返回响应。

不验证通信方身份带来的隐患：

- **无法确定请求发送至目标的 Web 服务器是否是按真实意图返回响应的那台服务器。有可能是已伪装的 Web 服务器。**

- **无法确定响应返回到的客户端是否是按真实意图接收响应的那个客户端。有可能是已伪装的客户端。**

- **无法确定正在通信的对方是否具备访问权限。因为某些 Web 服务器上保存着重要的信息，只想发给特定用户通信的权限。**

- **无法判定请求是来自何方、出自谁手。**

- **即使是无意义的请求也会照单全收。无法阻止海量请求下的 DoS 攻击（Denial of Service，拒绝服务攻击）。**

#### 查明对手的证书

虽然使用`HTTP`协议无法确定通信方，但使用`SSL`则可以。`SSL`不仅提供加密处理，而且还使用了一种被称为证书的手段，可用于确定通信方的身份。

证书由值得信任的第三方机构颁发，用以证明服务器和客户端是实际存在的。另外，伪造证书从技术角度来说是异常困难的一件事。所以只要能够确认通信方（服务器或客户端）持有的证书，即可判断通信方的真实意图。

客户端在开始通信之前先确认服务器的证书，确认后可以传输`HTTP`报文。而客户端在某些情况下也有自己的证书，该证书可以完成个人身份的认证。

### 无法证明报文完整性，可能已遭篡改

完整性指的是报文的准确性，无法验证报文的完整性，自然无法确定该报文有没有遭到恶意的篡改。

#### 接收到的内容可能有误

在`HTTP`请求报文发送到服务器和响应报文发送到客户端这段时间，`HTTP`没有办法知道内容在这段时间是否遭到了篡改，可能会遭受到**中间人攻击**（请求或响应在传输途中，遭攻击者拦截并篡改内容的攻击）。

#### 如何防止篡改

在`HTTPS`出现之前，就有针对完整性查看的方案出现。常用的就是`MD-5`和`SHA-1`散列值校验的方法和用来确认文件的数字签名方法。

提供文件下载服务的`Web`网站会提供相应的以`PGP`（Pretty Good Privacy，完美隐私）创建的数字签名及`MD5`算法生成的散列值。`PGP`是用来证明创建文件的数字签名，`MD5`是由单向函数生成的散列值。不论使用哪一种方法，都需要操纵客户端的用户本人亲自检查验证下载的文件是否就是原来服务器上的文件。浏览器无法自动帮用户检查。

如果这些用来验证的值本身被改写，用户也没有办法认识到，所以这种方法有很大的局限性。

> 数字签名（又称公钥数字签名）是只有信息的发送者才能产生的别人无法伪造的一段数字串，这段数字串同时也是对信息的发送者发送信息真实性的一个有效证明。

## HTTP + 加密 + 认证 + 完整性保护 = HTTPS

`HTTP`与加密处理，认证以及完整性保护结合后即是`HTTPS`。

对于前面提到的`HTTP`的三个安全问题，`HTTPS`都提供了对应的解决方案。

### HTTPS 是身披 SSL 外壳的 HTTP

`HTTPS`并不是新的协议，就是在`HTTP`的基础上，将通信接口部分用`SSL`或`TLS`协议替代而已。

> SSL：（Secure Socket Layer，安全套接字层），位于可靠的面向连接的网络层协议和应用层协议之间的一种协议层。SSL 通过互相认证、使用数字签名确保完整性、使用加密确保私密性，以实现客户端和服务器之间的安全通讯。该协议由两层组成：SSL 记录协议和 SSL 握手协议。SSL 是独立于 HTTP 的协议，所以不光是 HTTP 协议，其他运行在应用层的 SMTP 和 Telnet 等协议均可配合 SSL 协议使用。可以说 SSL 是当今世界上应用最为广泛的网络安全技术。
>
> TLS：(Transport Layer Security，传输层安全协议)，用于两个应用程序之间提供保密性和数据完整性，基于 SSL。该协议由两层组成：TLS 记录协议和 TLS 握手协议。

![](00145.jpeg)

加上了`SSL`协议，`HTTP`就化身为`HTTPS`，拥有了加密，身份确认（证书）和完整性保护这些功能。

### 相互交换密钥的公开密钥加密技术

提到加密，就不得不说加密方法。加密方法分为对称加密和非对称加密。

- 对称加密：指的就是加、解密使用的同是一串密钥，所以被称做对称加密。对称加密只有一个密钥作为私钥。
- 非对称加密：指的是加、解密使用不同的密钥，一把作为公开的公钥，另一把作为私钥。同一个人的公钥加密的信息，只有同一个人的私钥才能解密。

共享密钥加密（对称加密）有一个很明显的问题，想要对方解密，就必须发送密钥。但是密钥不能绝对安全的被对方获取，如果密钥可以安全传输，那数据自然也可以。

所以就有了公开密钥加密（非对称加密）。公开密钥加密使用一对非对称的密钥。一把叫做私有密钥（private key），另一把叫做公开密钥（public key）。顾名思义，私有密钥不能让其他任何人知道，而公开密钥则可以随意发布，任何人都可以获得。

使用公开密钥加密方式，发送密文的一方使用对方的公开密钥进行加密处理，对方收到被加密的信息后，再使用自己的私有密钥进行解密。利用这种方式，不需要发送用来解密的私有密钥，也不必担心密钥被攻击者窃听而盗走。

另外，要想根据密文和公开密钥，恢复到信息原文是异常困难的，因为解密过程就是在对离散对数进行求值，这并非轻而易举就能办到。退一步讲，如果能对一个非常大的整数做到快速地因式分解，那么密码破解还是存在希望的。但就目前的技术来看是不太现实的。

#### HTTPS 使用混合加密机制

`HTTPS`采用共享密钥加密和公开密钥加密两者并用的混合加密机制。若密钥能够实现安全交换，那么有可能会考虑仅使用公开密钥加密来通信。但是公开密钥加密与共享密钥加密相比，其处理速度要慢。

所以应充分利用两者各自的优势，将多种方法组合起来用于通信。在交换密钥环节使用公开密钥加密方式，之后的建立通信交换报文阶段则使用共享密钥加密方式。

这样只需要证明共享密钥是安全的就可以高效率的传输数据了，使用公开密钥加密交换建立通信时的共享密钥，解决了共享密钥传输的不足之处。

### 证明公开密钥正确性的证书

公开密钥使用明文传输，在第一次建立连接时由服务器传给客户端。客户端收到公钥后，自己生成对称加密的密钥。将对称加密密钥使用接收到的公钥进行加密发给服务器。服务器使用自己的私钥解密获得对称加密密钥，之后二者就进行对称加密传输。

但问题在于如果公钥被替换掉了，这下子公钥就变成其他人的公钥，且其他人也生成了自己替换的公钥的私钥。如果公钥被替换，中间人截获使用自己公钥加密的消息后，使用自己的私钥进行解密，再用当初的正常的公钥进行加密，发给服务器。就这样窃取了共享密钥，以后所有的消息都会被拦截解密。

为了解决这种情况，就有了数字证书认证机构的出现，该机构是客户端和服务器双方都十分信赖的。具体流程如下：

![](00150.jpeg)

这种方式下，通过证书上的数字签名验证就可以确认两件事：

- 认证服务器的公开密钥的是真实有效的数字证书认证机构
- 服务器的公开密钥是值得信赖的

公证机关的公开密钥不能被泄露，所以浏览器开发商在发布的时候，会将常用的认证机关的公开密钥在浏览器内部植入。

这样操作一番，唯一的区别就是之前是传输公钥，现在是传输证书。公钥可能会被替换，但是证书不存在被替换的情况，完全杜绝了混合加密存在的问题。

#### 可证明组织真实性的 EV SSL 证书

用来证明服务器背后运营企业是否真实存在的证书就是`EV SSL`证书，该证书严格规定了对运营组织是否真实的确认方针。

#### 用以确认客户端的客户端证书

`HTTPS`中还可以使用客户端证书。以客户端证书进行客户端认证，证明服务器正在通信的对方始终是预料之内的客户端，其作用跟服务器证书如出一辙。

现状是安全性极高的认证机构可颁发客户端证书但仅用于特殊用途的业务。比如那些可支撑客户端证书支出费用的业务。

#### 认证机构信誉第一

`SSL`机制中介认证机构之所以可行，是因为建立在其信用绝对可靠这一大前提下。

#### 由自认证机构颁发的证书称为自签名证书

如果使用`OpenSSL`这套开源程序，每个人都可以构建一套属于自己的认证机构，从而自己给自己颁发服务器证书。但该服务器证书在互联网上不可作为证书使用，似乎没什么帮助。

独立构建的认证机构叫做自认证机构，由自认证机构颁发的“无用”证书也被称为自签名证书。

浏览器访问该服务器时，会显示“无法确认连接安全性”或“该网站的安全证书存在问题”等警告消息。

由自认证机构颁发的服务器证书之所以不起作用，是因为它无法消除伪装的可能性。自认证机构能够产生的作用顶多也就是自己对外宣称“我是 xx”的这种程度。即使采用自签名证书，通过`SSL`加密之后，可能偶尔还会看见通信处在安全状态的提示，可那也是有问题的。因为 就算加密通信，也不能排除正在和已经过伪装的假服务器保持通信。

### HTTPS 安全通信机制

![](00153.jpeg)

> 步骤 1： 客户端通过发送 Client Hello 报文开始 SSL 通信。报文中包含客户端支持的 SSL 的指定版本、加密组件（Cipher Suite）列表（所使用的加密算法及密钥长度等）。
>
> 步骤 2： 服务器可进行 SSL 通信时，会以 Server Hello 报文作为应答。和客户端一样，在报文中包含 SSL 版本以及加密组件。服务器的加密组件内容是从接收到的客户端加密组>件内筛选出来的。
>
> 步骤 3： 之后服务器发送 Certificate 报文。报文中包含公开密钥证书。
>
> 步骤 4： 最后服务器发送 Server Hello Done 报文通知客户端，最初阶段的 SSL 握手协商部分结束。
>
> 步骤 5： SSL 第一次握手结束之后，客户端以 Client Key Exchange 报文作为回应。报文中包含通信加密中使用的一种被称为 Pre-master secret 的随机密码串。该报文已用步骤 3 中的公开密钥进行加密。
>
> 步骤 6： 接着客户端继续发送 Change Cipher Spec 报文。该报文会提示服务器，在此报文之后的通信会采用 Pre-master secret 密钥加密。
>
> 步骤 7： 客户端发送 Finished 报文。该报文包含连接至今全部报文的整体校验值。这次握手协商是否能够成功，要以服务器是否能够正确解密该报文作为判定标准。
>
> 步骤 8： 服务器同样发送 Change Cipher Spec 报文。
>
> 步骤 9： 服务器同样发送 Finished 报文。
>
> 步骤 10： 服务器和客户端的 Finished 报文交换完毕之后，SSL 连接就算建立完成。当然，通信会受到 SSL 的保护。从此处开始进行应用层协议的通信，即发送 HTTP 请求。
>
> 步骤 11： 应用层协议通信，即发送 HTTP 响应。
>
> 步骤 12： 最后由客户端断开连接。断开连接时，发送 close_notify 报文。上图做了一些省略，这步之后再发送 TCP FIN 报文来关闭与 TCP 的通信。
>
> 在以上流程中，应用层发送数据时会附加一种叫做 MAC（Message Authentication Code）的报文摘要。MAC 能够查知报文是否遭到篡改，从而保护报文的完整性。

#### SSL 和 TLS

`HTTPS`使用`SSL`（Secure Socket Layer）和`TLS`（Transport Layer Security）这两个协议。

> SSL 技术最初是由浏览器开发商网景通信公司率先倡导的，开发过 SSL3.0 之前的版本。目前主导权已转移到 IETF（Internet Engineering Task Force，Internet 工程任务组）的手中。
>
> IETF 以 SSL3.0 为基准，后又制定了 TLS1.0、TLS1.1 和 TLS1.2。TSL 是以 SSL 为原型开发的协议，有时会统一称该协议为 SSL。当前主流的版本是 SSL3.0 和 TLS1.0。
>
> 由于 SSL1.0 协议在设计之初被发现出了问题，就没有实际投入使用。SSL2.0 也被发现存在问题，所以很多浏览器直接废除了该协议版本。

#### SSL 对性能的影响

`HTTPS`比`HTTP`慢 2 到 100 倍。

对性能的影响主要在以下几方面：

- 协议交互所增加的网络 RTT（往返时延）
- 加密解密消耗资源，处理速度慢

![](00154.png)

由该图可以看出，首次请求最多可多出 7 个`RTT`。使用`HTTPS`通信过程如下：

- 三次握手建立连接（同 HTTP）
- 302 重定向
- 由于端口变化，必须重新进行三次握手
- TLS 加密套件协商和证书身份确认
- 浏览器获取证书开始验证，获取 CA 域名，没有命中缓存开始 DNS 解析，还要三次握手，使用 OCSP 在线证书状态协议查询证书有效状态。
- 密钥协商（TLS 握手阶段二）
- 数据传输 （同 HTTP）

`CPU`消耗主要体现在浏览器计算耗时和服务端计算耗时。

#### HTTPS 使用成本

- 与纯文本通信相比，加密通信会消耗更多的`CPU`及内存资源。如果每次通信都加密，会消耗相当多的资源，平摊到一台计算机上时，能够处理的请求数量必定也会随之减少。需要增加机器配置。

- 要进行`HTTPS`通信，证书是必不可少的。而使用的证书必须向认证机构（CA）购买。证书价格可能会根据不同的认证机构略有不同。

- 会降低用户访问速度，需要大量的优化。这也是需要成本的。
