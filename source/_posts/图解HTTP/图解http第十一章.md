---
title: Web 攻击技术与安全防范
tags:
  - web前端
  - 网络基础
categories:
  - 读书笔记
  - 图解HTTP
date: 2020-01-26 07:23:18
visible:
---

# Web 的攻击技术

![](011.png)

`Web`分为以上三层架构，安全问题多大发生在`Web`页（表示层）和业务逻辑层之间。

对于安全问题，`WASC`（Web Application Security Consortium）组织为`WWW`制定安全标准时总结了`Web`安全分类，指出了`Web`应用几乎所有可能遭受到的安全威胁。下面是几项主要的安全问题：

- `Authentication`验证：用来确认某用户，服务，或者是应用身份的攻击手段。
- `Authorization`授权：用来决定是否某用户，服务或是应用具有执行请求动作必要权限的攻击手段。
- `Client-Side Attacks`客户侧攻击：用来扰乱或是探测`Web`站点用户的攻击手段。
- `Command Execution`命令执行：在`Web`站点上执行远程命令的攻击手段。
- `Information Disclosure`信息泄露：用来获取`Web`站点具体系统信息的攻击手段。
- `Logic Attacks`逻辑性攻击：用来扰乱或是探测`Web`应用逻辑流程的攻击手段。

对于`Web`应用的攻击分为两种，主动攻击和被动攻击。

> 主动攻击（active attack）是指攻击者通过直接访问 Web 应用，把攻击代码传入的攻击模式。由于该模式是直接针对服务器上的资源进行攻击，因此攻击者需要能够访问到那些资源。
> 主动攻击模式里具有代表性的攻击是 SQL 注入攻击和 OS 命令注入攻击。
> 被动攻击（passive attack）是指利用圈套策略执行攻击代码的攻击模式。在被动攻击过程中，攻击者不直接对目标 Web 应用访问发起攻击。
> 被动攻击模式中具有代表性的攻击是跨站脚本攻击和跨站点请求伪造。

## Web 防御机制

`Web`三层安全防御机制为验证机制，会话管理机制，访问控制。

三层机制在下图中的不同部分得到体现。

![](012.png)

### 验证机制

验证机制是`Web`应用程序最简单的一种安全机制。是应用程序防御恶意攻击的核心机制。处于安全防御的最前沿，如果很容易被攻破，里面的安全防御也没什么用了。

当用户登录，首先是基于`HTML`表单的验证（看账号密码对不对），之后是一些多元机制（验证码等），最后可能会有客户端`SSL`证书验证。

#### 密码要求

许多`Web`应用没有对用户密码强度进行控制。

程序应该不允许用户进行暴力破解，即密码不能输入过多次。

#### 验证码技术

- 验证码应该真实有效，请求应该和验证码相关联。
- 验证码应有一定复杂度，可以对当前`OCR`工具进行限制。
- 防止有人远程破解验证码，例如使用图片等。

#### cookie 和会话检测

`cookie`在客户端，针对它的安全机制基本没用，可以很容易的被篡改。

#### 双因子认证

综合个人密码（知道什么）和手机（有什么）来达到双重认证的效果。

#### 忘记密码机制

忘记密码机制最重要的是忘记密码的业务逻辑问题。业务逻辑不缜密就可能被人利用。

还有一点就是各种敏感信息不要使用明文发送，不要过度依赖`MD5`加密。

### 会话管理概述

绝大多数`Web`应用程序中，会话管理机制是一个基本的安全组件。在应用程序执行登录功能时显得特别重要。它可以在用户通过请求提交他们的证书后，持续向应用程序保证任何特定用户身份的真实性。由于会话机制的重要作用，它们自然就成为恶意攻击的主要目标。

如果攻击者能破坏应用程序的会话管理，就能轻易避开验证机制，不用用户证书就可以伪装其他应用程序用户。

#### 会话令牌生成漏洞

会话令牌应该绝对随机和安全，不能随便被破解和按照规律推理出来。例如隐含序列（令牌之间有关联），时间依赖。

#### 会话终止攻击

尽可能缩短一个会话的寿命来避免攻击。一定要设置会话令牌的有效时间。且服务器与客户端失效对应的令牌一定要同步。

#### 会话劫持攻击

会话令牌应使用`HTTPS`传输。当使用`Cookie`时，设置好头部参数，强制使用`HTTPS`来传输`Cookie`。使用软会话过期，用户一段时间不操作失效令牌。提供完整的注销功能。

## SQL 注入

几乎每一个`Web`应用都需要数据库来保存信息，所以`Web`应用程序就会建立用户提交数据的`SQL`语句。如果建立这种语法的方式不安全，应用程序就很容易受到`SQL`注入的攻击。

用户会在表单中输入数据库操作语句，如果没有做一些转义或特殊处理的话，就容易被盗取信息。

`SQL`注入会探知数据库的具体结构，为进一步攻击做准备，泄露数据（机密信息）。也可以通过盗取数据，修改表数据来获取更高的权限。

### 参数化查询

参数化查询是对`SQL`注入根本性的防御策略，也叫做预处理语句，在用户输入`SQL`语句后，会进行如下两步：

- 指定查询结构，用户输入预留占位符
- 指定占位符的内容

## 跨站脚本攻击（XSS）

跨站脚本攻击（Cross Site Scripting），按道理来说应该叫`CSS`。但是名称被占掉了。只能叫`XSS`了。是一种经常出现在`Web`应用中的计算机安全漏洞。

它允许用户将代码植入到提供给其他用户使用的页面中，其他用户在观看网页时，恶意脚本就会执行。通常注入`HTML`或`JS`进行攻击。

浏览器和服务器没有对用户的输入进行过滤，导致用户注入的脚本嵌入到了页面中。由于浏览器无法识别这些恶意代码正常解析执行，攻击者的恶意操作被成功执行，比如可以获取用户的`cookie`数据然后发送给自己或者冒充正常用户向被攻击的服务器发送请求。

`XSS`两个层次是服务器端和浏览器端。协议就是`HTML/CSS/JavaScript`。对于服务器端来说，`html`是数据（字符串）；对于浏览器端来说，`html`是指令。`XSS`的原理，就是破坏`HTML/CSS/JavaScript`的构造。

### 危害

- 盗取用户账号
- 控制数据和账户行为
- 盗取有重要价值的资料

### XSS 分类

> 存储型 XSS：你发送一次带 XSS 代码的请求，以后这个页面的返回包里都会有 XSS 代码；
> 反射型 XSS：你发送一次带 XSS 代码的请求，只能在当前返回的数据包中发现 XSS 代码；
> DOM 型 XSS：你发送一次带 XSS 代码的请求，在返回包里压根儿就找不到 XSS 代码的影子；

#### 反射式

通过`URL`参数直接注入。发出请求时，`XSS`代码出现在`URL`中，作为输入提交到服务器端，服务端解析后根据参数动态生成`HTML`返回（可能会用到恶意脚本的代码），`XSS`代码随响应内容一起传回给浏览器，最后浏览器执行`XSS`代码。这个过程像一次反射，故叫做反射型`XSS`。经过后端，不经过数据库。诱饵是一个看起来可以信任的站点链接，用户点击后就会运行`XSS`脚本。

#### 持久型（存储式）

攻击者将带有恶意代码的内容发送给服务器（比如在论坛上发帖），服务器没有做过滤就将内容存储到数据库中，下次再请求这个页面的时候服务器从数据库中读取出相关的内容拼接到`HTML`上，浏览器收到之后解析执行。经过后端，经过数据库。

![](013.png)

#### DOM 型

基于`DOM`的攻击仅仅通过`JavaScript`的方式执行。不过后端，直接通过输入代码注入脚本攻击。简单去理解就是因为他输出位置在`DOM`，一般通过`URL`传入参数去触发。

![图片来自知乎](111.jpg)

### XSS 攻击载荷

#### 会话令牌

`XSS`攻击最普遍的方式，截取一名受害者的会话令牌，劫持他的会话，进而作为受害者的身份来使用应用程序，执行任意操作并占有该用户的账户。

#### 虚拟置换

这种攻击需要在一个`Web`应用程序页面注入恶意数据，从而向应用程序的用户传送误导性信息。

包括简单的向站点注入`HTML`，或者使用脚本注入精心设计的内容。

#### 注入木马

在易受攻击的应用程序注入实际运行的功能，目的在于欺骗终端用户执行某种有害操作（输入敏感数据）。随后将这些数据传给攻击者。

可以通过输入验证（不符合规则不让提交），输出编码（防止 XSS 将恶意代码放入返回值）。

## CSRF 攻击

跨站请求伪造（Cross-site request forgery），也被称为`one-click attack`或者`session riding`，通常缩写为`CSRF`或者`XSRF`，是一种挟制用户在当前已登录的`Web`应用程序上执行非本意的操作的攻击方法。

### CSRF 与 XSS 区别

`XSS`利用站点内的信任用户（受害者）进行攻击。`CSRF`通过伪装来自受信任用户的请求来利用受信任的网站。二者发出攻击的主体不同。

`CSRF`破坏力取决于受害者的权限。

### 例子

如果小明登录了一个金融网站，小红发现这个网站有个转账的`Bug`。小红发了一篇博客，里面嵌入了一个包含恶意脚本的图片标签，如果小明在打开金融网站的时候看到了这个博客，由于会话令牌是共享的，所以就会执行对应的命令（转账等）。

### 原因

- 浏览器会自动发送用户对话信息，用户没有感觉。
- 浏览器可以访问会话管理信息

### 预防

- 增加一些确认操作
- 重新认证，重要操作再次验证
- 使用`Token`，每次请求检查`Token`和用户`Session`中的`Token`是否一致

> Token：在计算机身份认证中是令牌（临时）的意思，在词法分析中是标记的意思。一般作为邀请、登录系统使用。

## 总结

图解`HTTP`这本书到此就全部看完了，对应的读书笔记也都全部结束，这是我第一本从头到尾读完的技术书籍。实不相瞒，还是挺爽的。接下来准备开读阮一峰的《ES6 入门》。
