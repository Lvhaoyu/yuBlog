---
title: 图解http第六章
date: 2020-01-12 14:07:29
tags:
  - web前端
  - 网络基础
categories:
  - 读书笔记
  - 图解HTTP
---

# HTTP 首部

`HTTP`请求和响应报文中必定存在`HTTP`首部。负责提供客户端和服务器分别处理请求和响应所需要的信息。

## HTTP 首部字段

### HTTP 首部字段传递重要的信息

`HTTP`首部字段是构成`HTTP`报文的要素之一。在客户端与服务器之间以`HTTP`协议进行通信的过程中，无论是请求还是响应都会使用首部字段，它能起到传递额外重要信息的作用。

使用首部字段是为了给浏览器和服务器提供报文主体大小、所使用的语言、认证信息等内容。

### HTTP 首部字段结构

`HTTP`首部字段由首部字段名和字段值构成，中间由冒号`:`分隔。

```
首部字段名: 字段值
```

单个字段名可以对应多个字段值，使用逗号`,`隔开。

```
KeepAlive: timeout = 15, max = 100
```

如果首部字段名称重复了，不同浏览器的处理并不相同，有些是处理第一个，有些是处理最后一个。

### 4 种 HTTP 首部类型

#### 通用首部字段（General Header Fields）

请求报文和响应报文两方都会使用的首部。

| **首部字段名**    | **说明**                   |
| ----------------- | -------------------------- |
| Cache-Control     | 控制缓存的行为             |
| Connection        | 逐跳首部、连接的管理       |
| Date              | 创建报文的日期时间         |
| Pragma            | 报文指令                   |
| Trailer           | 报文末端的首部一览         |
| Transfer-Encoding | 指定报文主体的传输编码方式 |
| Upgrade           | 升级为其他协议             |
| Via               | 代理服务器的相关信息       |
| Warning           | 错误通知                   |

#### 请求首部字段（Request Header Fields）

从客户端向服务器端发送请求报文时使用的首部。补充了请求的附加内容、客户端信息、响应内容相关优先级等信息。

| **首部字段名**      | **说明**                                        |
| ------------------- | ----------------------------------------------- |
| Accept              | 用户代理可处理的媒体类型                        |
| Accept-Charset      | 优先的字符集                                    |
| Accept-Encoding     | 优先的内容编码                                  |
| Accept-Language     | 优先的语言（自然语言）                          |
| Authorization       | Web 认证信息                                    |
| Expect              | 期待服务器的特定行为                            |
| From                | 用户的电子邮箱地址                              |
| Host                | 请求资源所在服务器                              |
| If-Match            | 比较实体标记（ETag）                            |
| If-Modified-Since   | 比较资源的更新时间                              |
| If-None-Match       | 比较实体标记（与 If-Match 相反）                |
| If-Range            | 资源未更新时发送实体 Byte 的范围请求            |
| If-Unmodified-Since | 比较资源的更新时间（与 If-Modified-Since 相反） |
| Max-Forwards        | 最大传输逐跳数                                  |
| Proxy-Authorization | 代理服务器要求客户端的认证信息                  |
| Range               | 实体的字节范围请求                              |
| Referer             | 对请求中 URI 的原始获取方                       |
| TE                  | 传输编码的优先级                                |
| User-Agent          | HTTP 客户端程序的信息                           |

#### 响应首部字段（Response Header Fields）

从服务器端向客户端返回响应报文时使用的首部。补充了响应的附加内容，也会要求客户端附加额外的内容信息。

| **首部字段名**     | **说明**                     |
| ------------------ | ---------------------------- |
| Accept-Ranges      | 是否接受字节范围请求         |
| Age                | 推算资源创建经过时间         |
| Etag               | 资源的匹配信息               |
| Location           | 令客户端重定向至指定 URI     |
| Proxy-Authenticate | 代理服务器对客户端的认证信息 |
| Retry-After        | 对再次发起请求的时机要求     |
| Server             | HTTP 服务器的安装信息        |
| Vary               | 代理服务器缓存的管理信息     |
| WWW-Authenticate   | 服务器对客户端的认证信息     |

#### 实体首部字段（Entity Header Fields）

针对请求报文和响应报文的实体部分使用的首部。补充了资源内容更新时间等与实体有关的信息。

| **首部字段名**   | **说明**                     |
| ---------------- | ---------------------------- |
| Allow            | 资源可支持的 HTTP 方法       |
| Content-Encoding | 实体主体适用的编码方式       |
| Content-Language | 实体主体的自然语言           |
| Content-Length   | 实体主体的大小（单位：字节） |
| Content-Location | 替代对应资源的 URI           |
| Content-MD5      | 实体主体的报文摘要           |
| Content-Range    | 实体主体的位置范围           |
| Content-Type     | 实体主体的媒体类型           |
| Expires          | 实体主体过期的日期时间       |
| Last-Modified    | 资源的最后修改日期时间       |

### 非 HTTP/1.1 首部字段

- Cookie：客户端向服务器发送的`Cookie`信息，请求字段
- Set-Cookie：由服务器端向客户端发送 cookie，响应字段
  - `Set-Cookie: <cookie名>=<cookie值>`
- Content-Disposition

![](Jietu20200112-165934.jpg)

### End-to-end 首部和 Hop-by-hop 首部

`HTTP`首部字段将定义成缓存代理和非缓存代理的行为，分成`2`种类型。

#### 端到端首部（End-to-end Header）

分在此类别中的首部会转发给请求 / 响应对应的最终接收目标，且必须保存在由缓存生成的响应中，另外规定它必须被转发。

#### 逐跳首部（Hop-by-hop Header）

分在此类别中的首部只对单次转发有效，会因通过缓存或代理而不再转发。`HTTP/1.1`和之后版本中，如果要使用`hop-by-hop`首部，需提供`Connection`首部字段。

除了以下字段，都是端到端首部：

- **Connection**

- **Keep-Alive**

- **Proxy-Authenticate**

- **Proxy-Authorization**

- **Trailer**

- **TE**

- **Transfer-Encoding**

- **Upgrade**

## HTTP/1.1 通用首部

### Cache-Control

该部分内容在[《图解 http 第五章》](https://www.lvhaoyu.cn/2020/01/10/%E5%9B%BE%E8%A7%A3http%E7%AC%AC%E4%BA%94%E7%AB%A0)中已经讲过了，就不再赘述了，补充一条。

#### Cache-Control 扩展

```
cache-extension token
```

```
Cache-Control: private, community="UCI"
```

通过`cache-extension`标记（token），可以扩展`Cache-Control`首部字段内的指令。

如上例，`Cache-Control`首部字段本身没有`community`这个指令。借助`extension tokens`实现了该指令的添加。如果缓存服务器不能理解`community`这个新指令，就会直接忽略。因此，`extension tokens`仅对能理解它的缓存服务器来说是有意义的。

### Connection

该字段可以用来控制两项内容：

- 控制不再转发给代理的首部字段

  - 该字段后边的值是首部出现过的字段名，告知代理不要讲该首部字段转发给服务器

- 管理持久连接
  - 持久连接在之前的文章中也讲过，在`HTTP/1.1`中，默认都是持久连接。如果服务端想断开连接，需要在`Connection`字段添加`Close`，之前版本的`HTTP`想建立持久连接需要在该字段加上`Keep-Alive`

### Date

顾名思义，就是表明时间，这个时间是创建`HTTP`报文的时间。

```
Date: Tue, 03 Jul 2012 04:40:59 GMT
```

### Pragma

这个字段是前朝余孽了，在`HTTP/1.1`的时代为了兼容老版本，还在使用。

```
Pragma: no-catch // 只在客户端报文中，要求所有的中间服务器不返回缓存的资源
```

### Trailer

首部字段`Trailer`会事先说明在报文主体后记录了哪些首部字段。允许发送方在分块发送的消息后面添加额外的元信息，这些元信息可能是随着消息主体的发送动态生成的，比如消息的完整性校验，消息的数字签名，或者消息经过处理之后的最终状态等。

该首部字段可以出现在分块信息挂载部分的消息首部。以下首部字段**不允许**出现（因为在首部会产生歧义）：

- 用于信息分帧的首部 (例如[`Transfer-Encoding`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Transfer-Encoding "Transfer-Encoding 消息首部指明了将 entity 安全传递给用户所采用的编码形式。") 和  [`Content-Length`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Length "Content-Length 是一个实体消息首部，用来指明发送给接收方的消息主体的大小，即用十进制数字表示的八位元组的数目。")),
- 用于路由用途的首部 (例如 [`Host`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Host "Host 请求头指明了服务器的域名（对于虚拟主机来说），以及（可选的）服务器监听的TCP端口号。"))，
- 请求修饰首部 (例如控制类和条件类的，如[`Cache-Control`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control " Cache-Control 通用消息头字段，被用于在http请求和响应中，通过指定指令来实现缓存机制。缓存指令是单向的，这意味着在请求中设置的指令，不一定被包含在响应中。")，<a class="new" rel="nofollow" title="此页面仍未被本地化, 期待您的翻译!">`Max-Forwards`</a>，或者 [`TE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/TE "TE 请求型头部用来指定用户代理希望使用的传输编码类型。(可以将其非正式称为 Accept-Transfer-Encoding， 这个名称显得更直观一些)。"))，
- 身份验证首部 (例如 [`Authorization`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Authorization "HTTP协议中的 Authorization 请求消息头含有服务器用于验证用户代理身份的凭证，通常会在服务器返回401 Unauthorized 状态码以及WWW-Authenticate 消息头之后在后续请求中发送此消息头。") 或者  [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie "响应首部 Set-Cookie 被用来由服务器端向客户端发送 cookie。"))，
- [`Content-Encoding`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Encoding "Content-Encoding 是一个实体消息首部，用于对特定媒体类型的数据进行压缩。当这个首部出现的时候，它的值表示消息主体进行了何种方式的内容编码转换。这个消息首部用来告知客户端应该怎样解码才能获取在 Content-Type 中标示的媒体类型内容。"), [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type "Content-Type 实体头部用于指示资源的MIME类型 media type 。"), [`Content-Range`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Range "在HTTP协议中，响应首部 Content-Range 显示的是一个数据片段在整个文件中的位置。")，以及  `Trailer`  自身。

```
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked
Trailer: Expires

7\r\n
Mozilla\r\n
9\r\n
Developer\r\n
7\r\n
Network\r\n
0\r\n
Expires: Wed, 21 Oct 2015 07:28:00 GMT\r\n
\r\n
```

### Upgrade

首部字段`Upgrade`用于检测`HTTP`协议及其他协议是否可使用更高的版本进行通信，其参数值可以用来指定一个完全不同的通信协议。使用首部字段`Upgrade`时，还需要额外指定 `Connection:Upgrade`。

如果请求成功，服务器返回`101 Switching Protocol`（协议切换）状态码表示服务器接收客户端升级协议的请求对协议进行切换，切换之后使用新协议进行通信。

如果不同意请求，就忽略`Upgrade`返回一个常规的状态码（和`Upgrade`无关）。

### Transfer-Encoding

规定了传输报文主体采用哪种编码形式，但是`HTTP/1.1`只可以使用`chunked`分块传输编码。

```
HTTP/1.1 200 OK
Date: Tue, 03 Jul 2012 04:40:56 GMT
Cache-Control: public, max-age=604800
Content-Type: text/javascript; charset=utf-8
Expires: Tue, 10 Jul 2012 04:40:56 GMT
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Encoding: gzip
Transfer-Encoding: chunked
Connection: keep-alive

cf0    ←16进制(10进制为3312)

...3312字节分块数据...

392    ←16进制(10进制为914)

...914字节分块数据...

0 // 必须以0结尾
```

### Via

使用首部字段`Via`是为了追踪客户端与服务器之间的请求和响应报文的传输路径。

在前一章代理和网关中提到过，每经过一个代理或网关，首部该字段会增加一个值（可以使用逗号`,`分隔，也可以增加一个同名字段）。传输请求和响应都需要加，内容就是记录代理服务器的信息（`HTTP`版本、请求`URI`、服务器代理软件服务版本号等）。

`Via`首部是为了追踪传输路径，所以经常会和`TRACE`方法一起使用。比如，代理服务器接收到由`TRACE`方法发送过来的请求（其中 `Max-Forwards: 0`）时，代理服务器就不能再转发该请求了。这种情况下，代理服务器会将自身的信息附加到`Via`首部后，返回该请求的响应。

### Warning

该首部通常会告知用户一些与缓存相关的问题的警告。

```
Warning: [警告码][警告的主机:端口号]“[警告内容]”([日期时间])
```

警告码（可自行扩展，仅供参考）如下：

| **警告码** | **警告内容** | **说明** |
| --- | --- | --- |
| 110 | Response is stale（响应已过期） | 代理返回已过期的资源 |
| 111 | Revalidation failed（再验证失败） | 代理再验证资源有效性时失败（服务器无法到达等原因） |
| 112 | Disconnection operation（断开连接操作） | 代理与互联网连接被故意切断 |
| 113 | Heuristic expiration（试探性过期） | 响应的使用期超过24小时（有效缓存的设定时间大于24小时的情况下） |
| 199 | Miscellaneous warning（杂项警告） | 任意的警告内容 |
| 214 | Transformation applied（使用了转换） | 代理对内容编码或媒体类型等执行了某些处理时 |
| 299 | Miscellaneous persistent warning（持久杂项警告） | 任意的警告内容 |

## 请求首部字段

### Accept

```
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
```

`Accept`首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可使用 `type/subtype`这种形式，一次指定多种媒体类型。

若想要给显示的媒体类型增加优先级，则使用`q=`来额外表示权重值 1，用分号（`;`）进行分隔。权重值`q`的范围是`0~1`（可精确到小数点后 3 位），且 1 为最大值。不指定权重 q 值时，默认权重为`q=1.0`。

### Accept-Charset

```
Accept-Charset: iso-8859-5, unicode-1-1;q=0.8
```

`Accept-Charset`首部字段用于内容协商机制的服务器协商，可用来通知服务器用户代理支持的字符集及字符集的相对优先顺序。另外，可一次性指定多种字符集。与首部字段`Accept`相同的是可用权重`q`值来表示相对优先级。

### Accept-Encoding

```
Accept-Encoding: gzip, deflate
```

该首部字段用来告知服务器用户代理支持的内容编码及内容编码的优先级顺序。可一次性指定多种内容编码，采用权重`q`值来表示相对优先级，这点与首部字段`Accept`相同。另外，也可使用星号（`*`）作为通配符，指定任意的编码格式。

### Accept-Language

```
Accept-Language: zh-cn,zh;q=0.7,en-us,en;q=0.3
```

首部字段`Accept-Language`用来告知服务器用户代理能够处理的自然语言集（指中文或英文等），以及自然语言集的相对优先级。可一次指定多种自然语言集。

和`Accept`首部字段一样，按权重值`q`来表示相对优先级。在上述图例中，客户端在服务器有中文版资源的情况下，会请求其返回中文版对应的响应，没有中文版时，则请求返回英文版响应。

### Authorization

```
Authorization: Basic dWVub3NlbjpwYXNzd29yZA==
```

首部字段`Authorization`是用来告知服务器，用户代理的认证信息（证书值）。通常，想要通过服务器认证的用户代理会在接收到返回的`401`状态码响应或`WWW-Authenticate`消息头之后，把首部字段`Authorization`加入请求中。

> `HTTP WWW-Authenticate`响应头定义了使用何种验证方式去获取对资源的连接。`WWW-Authenticate header`通常会和一个 `401 Unauthorized` 的响应一同被发送。

### Expect

```
Expect: 100-continue
```

客户端使用首部字段`Expect`来告知服务器，期望出现的某种特定行为。因服务器无法理解客户端的期望作出回应而发生错误时，会返回状态码`417 Expectation Failed`。

客户端可以利用该首部字段，写明所期望的扩展。如果客户端将在发送请求体之前等待`100`状态码的响应，则它必须发送一个`Expect`请求头，值为“100-continue”。如果客户端不打算发送请求体，就禁止发送值为“100-continue”的`Expect`请求头。

当接收到包含`Expect`的值为“100-continue”的请求头时，服务器必须响应`100`状态码并继续读取输入流，或者响应一个最终的状态码。就是服务器在收到请求后先发一个`100`给客户端，客户端收到之后，发送请求体给服务器处理。服务器禁止在发送`100`状态码响应前等待请求体。如果返回了一个最终的状态码，它可以终止传输连接或者可以继续读剩余的请求并丢弃，但禁止处理请求。

> HTTP 100 Continue 信息型状态响应码表示目前为止一切正常, 客户端应该继续请求, 如果已完成请求则忽略.

### From

```
From: xxx@xx.xx // E-mail地址
```

首部字段`From`用来告知服务器使用用户代理的用户的电子邮件地址。通常，其使用目的就是为了显示搜索引擎等用户代理的负责人的电子邮件联系方式。使用代理时，应尽可能包含`From`首部字段（但可能会因代理不同，将电子邮件地址记录在`User-Agent`首部字段内）

### Host

```
Host: www.xxx.com
```

首部字段`Host`会告知服务器，请求的资源所处的互联网主机名和端口号。`Host`首部字段在 `HTTP/1.1`规范内是唯一一个必须被包含在请求内的首部字段。

首部字段`Host`和以单台服务器分配多个域名的虚拟主机的工作机制有很密切的关联，这是首部字段`Host`必须存在的意义。

请求被发送至服务器时，请求中的主机名会用`IP`地址直接替换解决。但如果这时，相同的`IP`地址下部署运行着多个域名，那么服务器就会无法理解究竟是哪个域名对应的请求。因此，就需要使用首部字段`Host`来明确指出请求的主机名。若服务器未设定主机名，那直接发送一个空值即可。如下所示。

```
Host:
```

### If-Match（附带条件请求）

形如`If-xxx`这种样式的请求首部字段，都可称为条件请求。服务器接收到附带条件的请求后，只有判断指定条件为真时，才会执行请求。

```
If-Match: "123456" // 与Etag进行比对，一致执行请求，否则返回412（Precondition Failed ）
```

首部字段`If-Match`，属附带条件之一，它会告知服务器匹配资源所用的实体标记（`ETag`）值。这时的服务器无法使用弱`Etag`值。

还可以使用星号（`*`）指定`If-Match`的字段值。针对这种情况，服务器将会忽略`ETag`的值，只要资源存在就处理请求。

### If-Modified-Since

```
If-Modified-Since: Thu, 15 Apr 2004 00:00:00 GMT
```

如果在`If-Modified-Since`字段指定的日期时间后，资源发生了更新，服务器会接受请求。如果请求的资源都没有过更新，则返回状态码`304 Not Modified`的响应。用于确认代理或客户端拥有的本地资源的有效性，通过确认首部字段`Last-Modified`来确定。

### If-None-Match

只有在`If-None-Match`的字段值与`Etag`值不一致时，可处理该请求。与`If-Match`首部字段的作用相反。

在`GET`或`HEAD`方法中使用首部字段`If-None-Match`可获取最新的资源。因此，这与首部字段`If-Modified-Since`有些类似。

也可以使用星号（`*`）指定`If-None-Match`的字段值。针对这种情况，服务器将会忽略`ETag`的值，只要资源**不**存在就处理请求。

### If-Range

首部字段`If-Range`属于附带条件之一。它告知服务器若指定的`If-Range`字段值（`ETag`值或者时间）和请求资源的`ETag`值或时间相一致时，则作为范围请求处理。反之，则返回全体资源。

需要配合`Range`字段使用，如果进行范围请求未指定`If-Range`而是指定了其他附带条件，`Range`会失效，并且服务器返回`412 Precondition Failed`（前提条件失败），等待客户端再发一次请求。

### If-Unmodified-Since

```
If-Unmodified-Since: Thu, 03 Jul 2012 00:00:00 GMT
```

首部字段`If-Unmodified-Since`和首部字段`If-Modified-Since`的作用相反。它的作用的是告知服务器，指定的请求资源只有在字段值内指定的日期时间之后，未发生更新的情况下，才能处理请求。如果在指定日期时间后发生了更新，则以状态码`412 Precondition Failed`作为响应返回。

### Max-Forwards

```
Max-Forwards: 10 // 每次转发数值减1，当数值变为0返回响应
```

通过`TRACE`方法或`OPTIONS`方法，发送包含首部字段`Max-Forwards`的请求时，该字段以十进制整数形式指定可经过的服务器最大数目。服务器在往下一个服务器转发请求之前，`Max-Forwards`的值减 1 后重新赋值。当服务器接收到`Max-Forwards`值为 0 的请求时，则不再进行转发，而是直接返回响应。

使用`HTTP`协议通信时，请求可能会经过代理等多台服务器。途中，如果代理服务器由于某些原因导致请求转发失败，客户端也就等不到服务器返回的响应了。对此，我们无从可知。

可以灵活使用首部字段`Max-Forwards`，针对以上问题产生的原因展开调查。由于当 `Max-Forwards`字段值为 0 时，服务器就会立即返回响应，由此我们至少可以通过设置数值对以那台返回 0 的服务器为终点的传输路径的通信状况有所把握。

### Proxy-Authorization

```
Proxy-Authorization: Basic dGlwOjkpNLAGfFY5
```

接收到从代理服务器发来的认证质询时，客户端会发送包含首部字段`Proxy-Authorization`的请求，以告知服务器认证所需要的信息。

这个行为是与客户端和服务器之间的`HTTP`访问认证相类似的，不同之处在于，认证行为发生在客户端与代理之间。客户端与服务器之间的认证，使用首部字段`Authorization`可起到相同作用。

### Range

```
Range: bytes=5001-10000
```

对于只需获取部分资源的范围请求，包含首部字段`Range`即可告知服务器资源的指定范围。上面的示例表示请求获取从第 5001 字节至第 10000 字节的资源。

接收到附带`Range`首部字段请求的服务器，会在处理请求之后返回状态码为`206 Partial Content`的响应。无法处理该范围请求时，则会返回状态码`200 OK`的响应及全部资源。

### Referer

```
Referer: http://www.xxx.com/index.html
```

首部字段`Referer`会告知服务器请求的原始资源的`URI`。

客户端一般都会发送`Referer`首部字段给服务器。但当直接在浏览器的地址栏输入`URI`，或出于安全性的考虑时，也可以不发送该首部字段。

因为原始资源的`URI`中的查询字符串可能含有`ID`和密码等保密信息，要是写进`Referer`转发给其他服务器，则有可能导致保密信息的泄露。

### TE

```
TE: gzip, deflate;q=0.5
```

首部字段`TE`会告知服务器客户端能够处理响应的传输编码方式及相对优先级。它和首部字段 `Accept-Encoding`的功能很相像，但是用于传输编码。

首部字段`TE`除指定传输编码之外，还可以指定伴随`trailer`字段的分块传输编码的方式。应用后者时，只需把`trailers`赋值给该字段值。

```
TE: trailers
```

### User-Agent

```
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64; rv:13.0) Gecko/20100101 Firefox/13.0.1
```

首部字段`User-Agent`会将创建请求的浏览器和用户代理名称等信息传达给服务器。由网络爬虫发起请求时，有可能会在字段内添加爬虫作者的电子邮件地址。此外，如果请求经过代理，那么中间也很可能被添加上代理服务器的名称。

## 响应首部字段

### Accept-Ranges

```
Accept-Ranges: bytes / none
```

首部字段`Accept-Ranges`是用来告知客户端服务器是否能处理范围请求，以指定获取服务器端某个部分的资源。

可指定的字段值有两种，可处理范围请求时指定其为`bytes`，反之则指定其为`none`。

### Age

```
Age: 600
```

首部字段`Age`能告知客户端，源服务器在多久前创建了响应。字段值的单位为秒。

若创建该响应的服务器是缓存服务器，`Age`值是指缓存后的响应再次发起认证到认证完成的时间值。代理创建响应时必须加上首部字段`Age`。

### ETag

```
ETag: "82e22293907ce725faf67773957acd12"
```

首部字段`ETag`能告知客户端实体标识。它是一种可将资源以字符串形式做唯一性标识的方式。服务器会为每份资源分配对应的`ETag`值。

另外，当资源更新时，`ETag`值也需要更新。生成`ETag`值时，并没有统一的算法规则，而仅仅是由服务器来分配。

资源被缓存时，就会被分配唯一性标识。例如，当使用中文版的浏览器访问`http://www.google.com/`时，就会返回中文版对应的资源，而使用英文版的浏览器访问时，则会返回英文版对应的资源。两者的`URI`是相同的，所以仅凭`URI`指定缓存的资源是相当困难的。若在下载过程中出现连接中断、再连接的情况，都会依照`ETag`值来指定资源。

#### 强 ETag 值

```
ETag: "usagi-1234"
```

不论实体发生多么细微的变化都会改变其值。

#### 弱 ETag 值

```
ETag: W/"usagi-1234"
```

弱`ETag`值只用于提示资源是否相同。只有资源发生了根本改变，产生差异时才会改变`ETag`值。这时，会在字段值最开始处附加`W/`。

### Location

```
Location: http://www.usagidesign.jp/sample.html
```

使用首部字段`Location`可以将响应接收方引导至某个与请求`URI`位置不同的资源。

基本上，该字段会配合`3xx ：Redirection`的响应，提供重定向的`URI`。

几乎所有的浏览器在接收到包含首部字段`Location`的响应后，都会强制性地尝试对已提示的重定向资源的访问。

### Proxy-Authenticate

```
Proxy-Authenticate: Basic realm="Usagidesign Auth"
```

首部字段`Proxy-Authenticate`会把由代理服务器所要求的认证信息发送给客户端。

它与客户端和服务器之间的`HTTP`访问认证的行为相似，不同之处在于其认证行为是在客户端与代理之间进行的。而客户端与服务器之间进行认证时，首部字段`WWW-Authorization`有着相同的作用。

### Retry-After

```
Retry-After: 120
```

首部字段`Retry-After`告知客户端应该在多久之后再次发送请求。主要配合状态码`503 Service Unavailable`响应，或`3xx Redirect`响应一起使用。

字段值可以指定为具体的日期时间（`Wed, 04 Jul 2012 06：34：24 GMT`等格式），也可以是创建响应后的秒数。

### Server

```
Server: Apache/2.2.17 (Unix)
Server: Apache/2.2.6 (Unix) PHP/5.2.5
```

首部字段`Server`告知客户端当前服务器上安装的`HTTP`服务器应用程序的信息。不单单会标出服务器上的软件应用名称，还有可能包括版本号和安装时启用的可选项。

### Vary

```
Vary: Accept-Language
```

首部字段`Vary`可对缓存进行控制。源服务器会向代理服务器传达关于本地缓存使用方法的命令。

从代理服务器接收到源服务器返回包含`Vary`指定项的响应之后，若再要进行缓存，仅对请求中含有相同`Vary`指定首部字段的请求返回缓存。即使对相同资源发起请求，但由于`Vary`指定的首部字段不相同，因此必须要从源服务器重新获取资源。

### WWW-Authenticate

```
// realm 字段的字符串是为了辨别请求 URI 指定资源所受到的保护策略
WWW-Authenticate: Basic realm="Usagidesign Auth"
```

首部字段`WWW-Authenticate`用于`HTTP`访问认证。它会告知客户端适用于访问请求`URI`所指定资源的认证方案（`Basic`或是`Digest`）和带参数提示的质询（`challenge`）。状态码`401 Unauthorized`响应中，肯定带有首部字段`WWW-Authenticate`。

## 实体首部字段

在请求和响应两方的`HTTP`报文中都含有与实体相关的首部字段。

### Allow

```
Allow: GET, HEAD
```

首部字段`Allow`用于通知客户端能够支持`Request-URI`指定资源的所有`HTTP`方法。当服务器接收到不支持的`HTTP`方法时，会以状态码`405 Method Not Allowed`作为响应返回。与此同时，还会把所有能支持的`HTTP`方法写入首部字段`Allow`后返回。

### Content-Encoding

```
Content-Encoding: gzip
```

首部字段`Content-Encoding`会告知客户端服务器对实体的主体部分选用的内容编码方式。内容编码是指在不丢失实体信息的前提下所进行的压缩。

### Content-Language

```
Content-Language: zh-CN
```

首部字段`Content-Language`会告知客户端，实体主体使用的自然语言（指中文或英文等语言）。

### Content-Length

```
Content-Length: 15000
```

首部字段`Content-Length`表明了实体主体部分的大小（单位是字节）。对实体主体进行内容编码传输时，不能再使用`Content-Length`首部字段。

### Content-Location

```
Content-Location: http://www.hackr.jp/index-ja.html
```

首部字段`Content-Location`给出与报文主体部分相对应的`URI`。和首部字段`Location`不同，`Content-Location`表示的是报文主体返回资源对应的`URI`。

比如，对于使用首部字段`Accept-Language`的服务器驱动型请求，当返回的页面内容与实际请求的对象不同时，首部字段`Content-Location`内会写明`URI`。（访问`http://www.hackr.jp/`返回的对象却是`http://www.hackr.jp/index-ja.html`等类似情况）

### Content-MD5

```
Content-MD5: OGFkZDUwNGVhNGY3N2MxMDIwZmQ4NTBmY2IyTY==
```

首部字段`Content-MD5`是一串由`MD5`算法生成的值，其目的在于检查报文主体在传输过程中是否保持完整，以及确认传输到达。

对报文主体执行`MD5`算法获得的`128`位二进制数，再通过`Base64`编码后将结果写入 `Content-MD5`字段值。由于`HTTP`首部无法记录二进制值，所以要通过``Base64`编码处理。为确保报文的有效性，作为接收方的客户端会对报文主体再执行一次相同的`MD5`算法。计算出的值与字段值作比较后，即可判断出报文主体的准确性。

采用这种方法，对内容上的偶发性改变是无从查证的，也无法检测出恶意篡改。其中一个原因在于，内容如果能够被篡改，那么同时意味着`Content-MD5`也可重新计算然后被篡改。所以处在接收阶段的客户端是无法意识到报文主体以及首部字段`Content-MD5`是已经被篡改过的。

> **Base64**是一种基于 64 个可打印字符来表示[二进制数据](/wiki/%E4%BA%8C%E8%BF%9B%E5%88%B6 "二进制")的表示方法。由于![{\displaystyle 2^{6}=64}](https://wikimedia.org/api/rest_v1/media/math/render/svg/c4becc8d811901597b9807eccff60f0897e3701a)，所以每 6 个[比特](/wiki/%E4%BD%8D%E5%85%83 "比特")为一个单元，对应某个可打印字符。3 个[字节](/wiki/%E5%AD%97%E8%8A%82 "字节")有 24 个比特，对应于 4 个 Base64 单元，即 3 个字节可由 4 个可打印字符来表示。它可用来作为[电子邮件](/wiki/%E7%94%B5%E5%AD%90%E9%82%AE%E4%BB%B6 "电子邮件")的传输[编码](/wiki/%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81 "字符编码")。在 Base64 中的可打印字符包括[字母](/wiki/%E6%8B%89%E4%B8%81%E5%AD%97%E6%AF%8D "拉丁字母")`A-Z`、`a-z`、[数字](/wiki/%E6%95%B0%E5%AD%97 "数字")`0-9`，这样共有 62 个字符，此外两个可打印符号在不同的系统中而不同。一些如[uuencode](/wiki/Uuencode "Uuencode")的其他编码方法，和之后[BinHex](/w/index.php?title=BinHex&action=edit&redlink=1)（英语：[BinHex](https://en.wikipedia.org/wiki/BinHex "en:BinHex")）的版本使用不同的 64 字符集来代表 6 个二进制数字，但是不被称为 Base64。

### Content-Range

```
Content-Range: bytes 5001-10000/10000
```

针对范围请求，返回响应时使用的首部字段`Content-Range`，能告知客户端作为响应返回的实体的哪个部分符合范围请求。字段值以字节为单位，表示当前发送部分及整个实体大小。

### Content-Type

```
Content-Type: text/html; charset=UTF-8
```

首部字段`Content-Type`说明了实体主体内对象的媒体类型。和首部字段`Accept`一样，字段值用`type/subtype`形式赋值。

参数`charset`使用`iso-8859-1`或`euc-jp`等字符集进行赋值。

### Expires

```
Expires: Wed, 04 Jul 2012 08:26:05 GMT
```

首部字段`Expires`会将资源失效的日期告知客户端。缓存服务器在接收到含有首部字段 `Expires`的响应后，会以缓存来应答请求，在`Expires`字段值指定的时间之前，响应的副本会一直被保存。当超过指定的时间后，缓存服务器在请求发送过来时，会转向源服务器请求资源。

源服务器不希望缓存服务器对资源缓存时，最好在`Expires`字段内写入与首部字段`Date`相同的时间值。

但是，当首部字段`Cache-Control`有指定`max-age`指令时，比起首部字段`Expires`，会优先处理`max-age`指令。

### Last-Modified（响应首部）

```
Last-Modified: Wed, 23 May 2012 09:59:55 GMT
```

首部字段`Last-Modified`指明资源最终修改的时间。一般来说，这个值就是`Request-URI`指定资源最后一次被修改的日期和时间。但类似使用`CGI`脚本进行动态数据处理时，该值有可能会变成数据最终修改时的时间，因为脚本要修改一会嘛，所以是开始修改的时间。

> 公共网关接口（Common Gateway Interface，CGI）是 Web 服务器运行时外部程序的规范，按 CGI 编写的程序可以扩展服务器功能。 CGI 应用程序能与浏览器进行交互，还可通过数据 API 与数据库服务器等外部数据源进行通信，从数据库服务器中获取数据。
>
> > CGI 把用户传递过来的数据转变成一个`k－v`的字典。这个字典中不光有用户的数据，还有 HTTP 协议的参数。它做的就是把数据，组织成一个固定结构形式的数据。

## 为 Cookie 服务的首部字段

> Cookie 的工作机制是用户识别及状态管理。Web 网站为了管理用户的状态会通过 Web 浏览器，把一些数据临时写入用户的计算机内。接着当用户访问该 Web 网站时，可通过通信方式取回之前发放的 Cookie。
> 调用 Cookie 时，由于可校验 Cookie 的有效期，以及发送方的域、路径、协议等信息，所以正规发布的 Cookie 内的数据不会因来自其他 Web 站点和攻击者的攻击而泄露。

### Set-Cookie（响应首部）

```
Set-Cookie: status=enable; expires=Tue, 05 Jul 2011 07:26:31 GMT; path=/; domain=.hackr.jp;
```

服务器告知客户端开始状态管理所使用的`Cookie`信息，一旦`Cookie`从服务器端发送至客户端，服务器端就不存在可以显式删除`Cookie`的方法。但可通过覆盖已过期的`Cookie`，实现对客户端`Cookie`的实质性删除操作。

字段值如下：

| **属性**    | **说明**        |
| ---         | ---             |
| NAME=VALUE   | 赋予 Cookie 的名称和其值（必需项） |
| expires=DATE | Cookie 的有效期（若不明确指定则默认为浏览器关闭前为止）|
| path=PATH    | 用于限制指定 Cookie 的发送范围的文件目录。（若不指定则默认为文档所在的文件目录）|
| domain=域名  | 作为 Cookie 适用对象的域名属性指定的域名，通过该属性指定的域名可做到与结尾匹配一致。 （若不指定则默认为创建 Cookie 的服务器的域名，不指定比较安全） |
| Secure       | 仅在 HTTPS 安全通信时才会发送 Cookie  |
| HttpOnly     | 加以限制，使 Cookie 不能被 JavaScript 脚本访问 |

### domain

当指定`example.com`后，除`example.com`以外，`www.example.com`或 `www2.example.com`等都可以发送`Cookie`。所以不指定比较安全。

### secure

```
Set-Cookie: name=value; secure
```

添加该属性的`Cookie`仅在`HTTPS`的状态下进行回收，`HTTP`不会回收，省略该属性的话二者都会回收。

### HttpOnly

`Cookie`的`HttpOnly`属性是`Cookie`的扩展功能，它使`JavaScript`脚本无法获得 `Cookie`。其主要目的为防止跨站脚本攻击（Cross-site scripting，XSS）对`Cookie`的信息窃取。

```
// document.cookie 就无法读取拥有 HttpOnly 属性的 Cookie 的内容了
Set-Cookie: name=value; HttpOnly
```

### Cookie（请求首部）

```
Cookie: status=enable
```

首部字段`Cookie`会告知服务器，当客户端想获得`HTTP`状态管理支持时，就会在请求中包含从服务器接收到的`Cookie`。接收到多个`Cookie`时，同样可以以多个`Cookie`形式发送。

## 其他首部字段

`HTTP`首部字段是可以自行扩展的。所以在`Web`服务器和浏览器的应用上，会出现各种非标准的首部字段。

### X-Frame-Options

```
X-Frame-Options: DENY

// 对 apache2.conf 的配置实例
<IfModule mod_headers.c>
   Header append X-FRAME-OPTIONS "SAMEORIGIN"
</IfModule>
```

首部字段`X-Frame-Options`属于`HTTP`响应首部，用于控制网站内容在其他`Web`网站的 `Frame`标签内的显示问题。其主要目的是为了防止点击劫持（clickjacking）攻击。

> 点击劫持：攻击者使用一个或多个透明的 iframe 覆盖在一个正常的网页上，然后诱使用户在该网页上进行操作，当用户在不知情的情况下点击透明的 iframe 页面时，用户的操作已经被劫持到攻击者事先设计好的恶意按钮或链接上。

首部字段`X-Frame-Options`有以下两个可指定的字段值。

- DENY ：拒绝
- SAMEORIGIN ：仅同源域名下的页面（Top-level-browsing-context）匹配时许可。（比如，当指定`http://hackr.jp/sample.html`页面为`SAMEORIGIN`时，那么`hackr.jp`上所有页面的`frame`都被允许可加载该页面，而`example.com`等其他域名的页面就不行了）

### X-XSS-Protection

```
X-XSS-Protection: 1
```

首部字段`X-XSS-Protection`属于`HTTP`响应首部，它是针对跨站脚本攻击（XSS）的一种对策，用于控制浏览器`XSS`防护机制的开关。

首部字段`X-XSS-Protection`可指定的字段值如下。

- 0 ：将 XSS 过滤设置成无效状态

- 1 ：将 XSS 过滤设置成有效状态

### DNT

```
DNT: 1
```

首部字段`DNT`属于`HTTP`请求首部，其中`DNT`是`Do Not Track`的简称，意为拒绝个人信息被收集，是表示拒绝被精准广告追踪的一种方法。

首部字段`DNT`可指定的字段值如下。

- 0 ：同意被追踪

- 1 ：拒绝被追踪

由于首部字段`DNT`的功能具备有效性，所以`Web`服务器需要对`DNT`做对应的支持。

### P3P

```
P3P: CP="CAO DSP LAW CURa ADMa DEVa TAIa PSAa PSDa IVAa IVDa OUR BUS IND UNI COM NAV INT"
```

首部字段`P3P`属于`HTTP`相应首部，通过利用`P3P`（The Platform for Privacy Preferences，在线隐私偏好平台）技术，可以让`Web`网站上的个人隐私变成一种仅供程序可理解的形式，以达到保护用户隐私的目的。

要进行`P3P`的设定，需按以下操作步骤进行。

步骤 1：创建`P3P`隐私

步骤 2：创建`P3P`隐私对照文件后，保存命名在`/w3c/p3p.xml`

步骤 3：从`P3P`隐私中新建`Compact policies`后，输出到`HTTP`响应中

> 协议中对 X- 前缀的废除
>
> 在 HTTP 等多种协议中，通过给非标准参数加上前缀 X-，来区别于标准参数，并使那些非标准的参数作为扩展变成可能。但是这种简单粗暴的做法有百害而无一益，因此在“RFC 6648 - Deprecating the "X-" Prefix and Similar Constructs in Application Protocols”中提议停止该做法。
>
> 然而，对已经在使用中的 X- 前缀来说，不应该要求其变更。
