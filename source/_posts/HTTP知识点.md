---
title: 图解 HTTP 笔记
date: 2019-08-25 17:45:12
tags: 
    - HTTP
    - 计算机网络
categories: 基础
---
# 第一章 了解 Web 及网络基础

## IP
IP(Internet Protocol) 网际协议位于网络层。IP 地址指明了节点被分配到的地址，MAC 地址是指网卡所属的固定地址。IP 地址可以和 MAC 地址配对。IP 地址可变换，但 MAC 地址基本上不会改。ARP 是一种用以地址解析的协议，根据通信放的 IP 地址就可以反查出对应的 MAC 地址。

## TCP
TCP 位于传输层，提供可靠的字节流服务。字节流服务指，为了方便传输，将大块数据分割成以报文段为单位的数据包管理。可靠的传输服务指，能够把数据准确可靠的传给对方。

## URI
URI(Uniform Resource Identifier)
- Uniform：规定统一的格式可方便处理多种不同类型的资源，而不用根据上下文环境来识别资源指定的访问方式。
- Resource：资源的定义是“可标识的任何东西”
- Identifider：表示可标识的对象。也称为标识符。

# 第二章 简单的 HTTP 协议 

## HTTP 方法
- GET：获取资源。如果请求的资源是文本，那就保持原样返回；如果是像 CGI（Common Gateway Interface，通用网关接口）那样的程序，则返回经过执行后的输出结果。
- POST：传输实体主体
- PUT：传输文件。PUT 方法用来传输文件。就像 FTP 协议的文件上传一样，要求在请求报文的主体中包含文件内容，然后保存到请求 URI 指定的位置。
- HEAD：获得报文首部。HEAD 方法和 GET 方法一样，只是不返回报文主体部分。用于确认 URI 的有效性及资源更新的日期时间等。
- DELETE：删除文件。DELETE 方法用来删除文件，是与 PUT 相反的方法。DELETE 方法按请求 URI 删除指定的资源。
- OPTIONS：询问支持的方法.OPTIONS 方法用来查询针对请求 URI 指定的资源支持的方法。
- TRACE：追踪路径。TRACE 方法是让 Web 服务器端将之前的请求通信环回给客户端的方法。
- CONNECT：要求用隧道协议连接代理。CONNECT 方法要求在与代理服务器通信时建立隧道，实现用隧道协议进行 TCP 通信。主要使用 SSL（Secure Sockets Layer，安全套接层）和 TLS（Transport Layer Security，传输层安全）协议把通信内容加 密后经网络隧道传输。

## 持久连接
HTTP/1.1 和一部分的 HTTP/1.0 想出了持久连接（HTTP Persistent Connections，也称为 HTTP keep-alive 或 HTTP connection reuse）的方法。持久连接的特点是，只要任意一端没有明确提出断开连接，则保持 TCP 连接状态。

在 HTTP/1.1 中，所有的连接默认都是持久连接。

## 管线化
持久连接使得多数请求以管线化（pipelining）方式发送成为可能。从前发送请求后需等待并收到响应，才能发送下一个请求。管线化技术出现后，不用等待响应亦可直接发送下一个请求。

但是 HTTP 请求和响应没有序号标识，无法将乱序的响应与请求关联起来，服务端就必须按照请求发送的顺序返回响应，如果一个响应延迟了，那么其后续的响应都会被延迟，直到队头的响应送达。

# 第三章 HTTP 报文内的 HTTP 信息

## 请求
请求报文是由请求方法、请求 URI、协议版本、可选的请求首部字段和内容实体构成的

## 响应
响应报文由协议版本、状态码、用以解释状态码的原因短语、可选的响应首部字段以及实体主体构成。

## 编码提升传输速度
内容编码指明应用在实体内容上的编码格式，并保持实体信息原样压缩。内容编码后的实体由客户端接收并负责解码。

### 分割发送的分块传输编码
在 HTTP 通信过程中，请求的编码实体资源尚未全部传输完成之前，浏览器无法显示请求页面。在传输大容量数据时，通过把数据分割成多块，能够让浏览器逐步显示页面。

这种把实体主体分块的功能称为分块传输编码（Chunked Transfer Coding）。

## 发送多种数据的多部分对象集合
HTTP 协议中采纳了多部分对象集合，发送的一份报文主体内可含有多类型实体。通常是在图片或文本文件等上传时使用。

在 HTTP 报文中使用多部分对象集合时，需要在首部字段里加上 Content-type。

## 获取部分内容的范围请求 
执行范围请求时，会用到首部字段 Range 来指定资源的 byte 范围。

## 内容协商返回最合适的内容
内容协商机制是指客户端和服务器端就响应的资源内容进行交涉，然后提供给客户端最为适合的资源。内容协商会以响应资源的语言、字符集、编码方式等作为判断的基准。

# 第四章 返回结果的 HTTP 状态码

## 2XX 成功
状态码|原因短语
---|---
200|OK
204|No Content
206|Partial Content

## 3XX 重定向
状态码|原因短语
---|---
301|Moved Permanently
302|Found
303|See Other
304|Not Modified
307|Temporary Redirect

### 301 Moved Permanently
永久性重定向。该状态码表示请求的资源已被分配了新的 URI，以后应使用资源现在所指的 URI

### 302 Found
临时性重定向。该状态码表示请求的资源已被分配了新的 URI，希望用户（本次）能使用新的 URI 访问。

### 303 See Other
303 状态码和 302 Found 状态码有着相同的功能，但 303 状态码明确表示客户端应当采用 GET 方法获取资源，这点与 302 状态码有区别。

### 304 Not Modified
该状态码表示客户端发送附带条件的请求时，服务器端允许请求访问资源，但未满足条件的情况。304 状态码返回时，不包含任何响应的主体部分。304 虽然被划分在 3XX 类别中，但是和重定向没有关系。

### 307 Temporary Redirect
307 会遵照浏览器标准，不会从 POST 变成 GET。但是，对于处理响应时的行为，每种浏览器有可能出现不同的情况。

## 4XX 客户端错误
状态码|原因短语
---|---
400|Bad Request
401|Unauthorized
403|Fobidden
404|Not Found

### 401 Unauthorized
该状态码表示发送的请求需要有通过 HTTP 认证（BASIC 认证、DIGEST 认证）的认证信息。另外若之前已进行过 1 次请求，则表示用户认证失败。

## 500 服务器错误
状态码|原因短语
---|---
500|Internal Server Error
503|Service Unavailable

### 503 Service Unavailable
该状态码表明服务器暂时处于超负载或正在进行停机维护，现在无法处理请求。如果事先得知解除以上状况需要的时间，最好写入 RetryAfter 首部字段再返回给客户端。

# 第五章 与 HTTP 协作的 Web 服务器

## 用单台虚拟主机实现多个域名
在相同的 IP 地址下，由于虚拟主机可以寄存多个不同主机名和域名的 Web 网站，因此在发送 HTTP 请求时，必须在 Host 首部内完整指定主机名或域名的 URI。

## 通信数据转发程序 ：代理、网关、隧道

### 代理
代理是一种有转发功能的应用程序，它扮演了位于服务器和客户端“中间人”的角色，接收由客户端发送的请求并转发给服务器，同时也接收服务器返回的响应并转发给客户端。

每次通过代理服务器转发请求或响应时，会追加写入 Via 首部信息。

通过设置组织内部的代理服务器可做到针对特定 URI 的访问控制。

使用代理服务器的理由有：
- 利用缓存技术（稍后讲解）减少网络带宽的流量
- 组织内部针对特定网站的访问控制，以获取访问日志为主要目的

### 缓存代理
代理转发响应时，缓存代理（Caching Proxy）会预先将资源的副本（缓存）保存在代理服务器上。当代理再次接收到对相同资源的请求时，就可以不从源服务器那里获取资源，而是将之前缓存的资源作为响应返回。

### 透明代理
转发请求或响应时，不对报文做任何加工的代理类型被称为透明代理（Transparent Proxy）。反之，对报文内容进行加工的代理被称为非透明代理。

### 网关
网关是转发其他服务器通信数据的服务器，接收从客户端发送来的请求时，它就像自己拥有资源的源服务器一样对请求进行处理。有时客户端可能都不会察觉，自己的通信目标是一个网关。

利用网关可以由 HTTP 请求转化为其他协议通信。网关的工作机制和代理十分相似。而网关能使通信线路上的服务器提供非 HTTP 协议服务。

利用网关能提高通信的安全性，因为可以在客户端与网关之间的通信线路上加密以确保连接的安全。比如，网关可以连接数据库，使用 SQL 语句查询数据。另外，在 Web 购物网站上进行信用卡结算时，网关可以和信用卡结算系统联动。

### 隧道
隧道是在相隔甚远的客户端和服务器两者之间进行中转，并保持双方通信连接的应用程序。

隧道可按要求建立起一条与其他服务器的通信线路，届时使用 SSL 等加密手段进行通信。隧道的目的是确保客户端能与服务器进行安全的通信。隧道本身不会去解析 HTTP 请求。也就是说，请求保持原样中转给之后的服务器。隧道会在通信双方断开连接时结束。

## 保存资源的缓存
缓存是指代理服务器或客户端本地磁盘内保存的资源副本。利用缓存可减少对源服务器的访问，因此也就节省了通信流量和通信时间。

缓存服务器是代理服务器的一种，并归类在缓存代理类型中。换句话说，当代理转发从服务器返回的响应时，代理服务器将会保存一份资源的副本。

### 缓存的有效期限

### 客户端缓存

# HTTP 首部

## 通用首部字段

### Cache-Control
Cache-Control: private, max-age=0, no-cache，指令的参数是可选的，多个指令之间通过“,”分隔。首部字段 Cache-Control 的指令可用于请求及响应时。

#### no-cache
客户端发送的请求中如果包含 no-cache 指令，则表示客户端将不会接收缓存过的响应。于是，“中间”的缓存服务器必须把客户端请求转发给源服务器。

如果服务器返回的响应中包含 no-cache 指令，那么缓存服务器不能对资源进行缓存。源服务器以后也将不再对缓存服务器请求中提出的资源有效性进行确认，且禁止其对响应资源进行缓存操作。

no-cache 代表**不缓存过期的资源**，缓存会向服务器进行有效期确认后处理资源。no-sotre 才是真正地不进行缓存。

#### s-maxage
 s-maxage 指令只适用于供多位用户使用的公共缓存服务器(一般指代理)。另外，当使用 s-maxage 指令后，则直接忽略对 Expires 首部字段及 max-age 指令的处理。

#### max-age
当客户端发送的请求中包含 max-age 指令时，如果判定缓存资源的缓存时间数值比指定时间的数值更小，那么客户端就接收缓存的资源。另外，当指定 max-age 值为 0，那么缓存服务器通常需要将请求转发给源服务器。

当服务器返回的响应中包含 max-age 指令时，缓存服务器将不对资源的有效性再作确认，而 max-age 数值代表资源保存为缓存的最长时间。

应用 HTTP/1.1 版本的缓存服务器遇到同时存在 Expires 首部字段的情况时，会优先处理 max-age 指令，而忽略掉 Expires 首部字段。而 HTTP/1.0 版本的缓存服务器的情况却相反，max-age 指令会被忽略掉。

#### min-fresh
min-fresh 指令要求缓存服务器返回至少还未过指定时间的缓存资源。

比如，当指定 min-fresh 为 60 秒后，过了 60 秒的资源都无法作为响应返回了。

#### no-transform
使用 no-transform 指令规定无论是在请求还是响应中，缓存都不能改变实体主体的媒体类型。

这样做可防止缓存或代理压缩图片等类似操作。

### Connection

#### 控制不再转发给代理的首部字段
Connection: 不再转发的首部字段名。

在客户端发送请求和服务器返回响应内，使用 Connection 首部字段，可控制不再转发给代理的首部字段。

#### 管理持久连接
HTTP/1.1 版本的默认连接都是持久连接。为此，客户端会在持久连接上连续发送请求。当服务器端想明确断开连接时，则指定 Connection 首部字段的值为 Close。

### Pragma
Pragma 是 HTTP/1.1 之前版本的历史遗留字段，仅作为与 HTTP/1.0 的向后兼容而定义。

规范定义的形式唯一，Pragma: no-cache。

通常发送的请求会同时含有下面两个首部字段。
```
Cache-Control: no-cache
Pragma: no-cache
```
### Upgrade
首部字段 Upgrade 用于检测 HTTP 协议及其他协议是否可使用更高的版本进行通信，其参数值可以用来指定一个完全不同的通信协议。

使用首部字段 Upgrade 时，还需要额外指定 Connection:Upgrade。

### Via
使用首部字段 Via 是为了追踪客户端与服务器之间的请求和响应报文的传输路径。

报文经过代理或网关时，会先在首部字段 Via 中附加该服务器的信息，然后再进行转发。这个做法和 traceroute 及电子邮件的 Received 首部的工作机制很类似。

首部字段 Via 不仅用于追踪报文的转发，还可避免请求回环的发生。所以必须在经过代理时附加该首部字段内容。

## 请求首部字段

### Accept
Accept 首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可使用 type/subtype 这种形式，一次指定多种媒体类型。

若想要给显示的媒体类型增加优先级，则使用 q= 来额外表示权重值 1，用分号（;）进行分隔。权重值 q 的范围是 0~1（可精确到小数点后 3 位），且 1 为最大值。不指定权重 q 值时，默认权重为 q=1.0。

### Authorization
首部字段 Authorization 是用来告知服务器，用户代理的认证信息（证书值）。通常，想要通过服务器认证的用户代理会在接收到返回的 401 状态码响应后，把首部字段 Authorization 加入请求中。共用缓存在接收到含有 Authorization 首部字段的请求时的操作处理会略有差异。

### Host
首部字段 Host 会告知服务器，请求的资源所处的互联网主机名和端口号。Host 首部字段在 HTTP/1.1 规范内是唯一一个必须被包含在请求内的首部字段。

请求被发送至服务器时，请求中的主机名会用 IP 地址直接替换解决。但如果这时，相同的 IP 地址下部署运行着多个域名，那么服务器就会无法理解究竟是哪个域名对应的请求。因此，就需要使用首部字段 Host 来明确指出请求的主机名。若服务器未设定主机名，那直接发送一个空值即可。

### If-Match
服务器会比对 If-Match 的字段值和资源的 ETag 值，仅当两者一致时，才会执行请求。反之，则返回状态码 412 Precondition Failed 的响应。

还可以使用星号（*）指定 If-Match 的字段值。针对这种情况，服务器将会忽略 ETag 的值，只要资源存在就处理请求。

### If-Modified-Since
首部字段 If-Modified-Since，属附带条件之一，它会告知服务器若 If-Modified-Since 字段值早于资源的更新时间，则希望能处理该请求。而在指定 If-Modified-Since 字段值的日期时间之后，如果请求的资源都没有过更新，则返回状态码 304 Not Modified 的响应。

If-Modified-Since 用于确认代理或客户端拥有的本地资源的有效性。获取资源的更新日期时间，可通过确认首部字段 Last-Modified 来确定。

### If-None-Match
与 If-Match 首部字段的作用相反。

在 GET 或 HEAD 方法中使用首部字段 If-None-Match 可获取最新的资源。因此，这与使用首部字段 If-Modified-Since 时有些类似。

### If-Range
首部字段 If-Range 属于附带条件之一。它告知服务器若指定的 If-Range 字段值（ETag 值或者时间）和请求资源的 ETag 值或时间相一致时，则作为范围请求处理。反之，则返回全体资源。

### Max-Forwards
每次转发数值减 1。当数值变 0 时返回响应

### Range
对于只需获取部分资源的范围请求，包含首部字段 Range 即可告知服务器资源的指定范围。

接收到附带 Range 首部字段请求的服务器，会在处理请求之后返回状态码为 206 Partial Content 的响应。无法处理该范围请求时，则会返回状态码 200 OK 的响应及全部资源.

### Referer
首部字段 Referer 会告知服务器请求的原始资源的 URI。

### User-Agent
首部字段 User-Agent 会将创建请求的浏览器和用户代理名称等信息传达给服务器。

由网络爬虫发起请求时，有可能会在字段内添加爬虫作者的电子邮件地址。此外，如果请求经过代理，那么中间也很可能被添加上代理服务器的名称。

## 响应首部字段

### Accept-Ranges
首部字段 Accept-Ranges 是用来告知客户端服务器是否能处理范围请求，以指定获取服务器端某个部分的资源。

可指定的字段值有两种，可处理范围请求时指定其为 bytes，反之则指定其为 none。

### Age
首部字段 Age 能告知客户端，源服务器在多久前创建了响应。字段值的单位为秒。

若创建该响应的服务器是缓存服务器，Age 值是指缓存后的响应再次发起认证到认证完成的时间值。**代理**创建响应时**必须**加上首部字段 Age。

### ETag
首部字段 ETag 能告知客户端实体标识。它是一种可将资源以字符串形式做唯一性标识的方式。服务器会为每份资源分配对应的 ETag 值。

另外，当资源更新时，ETag 值也需要更新。生成 ETag 值时，并没有统一的算法规则，而仅仅是由服务器来分配。

资源被缓存时，就会被分配唯一性标识。例如，当使用中文版的浏览器访问 http://www.google.com/ 时，就会返回中文版对应的资源，而使用英文版的浏览器访问时，则会返回英文版对应的资源。两者的 URI 是相同的，所以仅凭 URI 指定缓存的资源是相当困难的。若在下载过程中出现连接中断、再连接的情况，都会依照 ETag 值来指定资源。

### Location
使用首部字段 Location 可以将响应接收方引导至某个与请求 URI 位置不同的资源。

基本上，该字段会配合 3xx ：Redirection 的响应，提供重定向的 URI。

几乎所有的浏览器在接收到包含首部字段 Location 的响应后，都会强制性地尝试对已提示的重定向资源的访问。

### Server
首部字段 Server 告知客户端当前服务器上安装的 HTTP 服务器应用程序的信息。不单单会标出服务器上的软件应用名称，还有可能包括版本号和安装时启用的可选项。
`Server: Apache/2.2.6 (Unix) PHP/5.2.5`

### Vary
从代理服务器接收到源服务器返回包含 Vary 指定项的响应之后，若再要进行缓存，仅对请求中含有相同 Vary 指定首部字段的请求返回缓存。即使对相同资源发起请求，但由于 Vary 指定的首部字段不相同，因此必须要从源服务器重新获取资源。

## 实体首部字段

### Allow
首部字段 Allow 用于通知客户端能够支持 Request-URI 指定资源的所有 HTTP 方法。当服务器接收到不支持的 HTTP 方法时，会以状态码 405 Method Not Allowed 作为响应返回。与此同时，还会把所有能支持的 HTTP 方法写入首部字段 Allow 后返回。

### Expires
首部字段 Expires 会将资源失效的日期告知客户端。缓存服务器在接收到含有首部字段 Expires 的响应后，会以缓存来应答请求，在 Expires 字段值指定的时间之前，响应的副本会一直被保存。当超过指定的时间后，缓存服务器在请求发送过来时，会转向源服务器请求资源。

源服务器不希望缓存服务器对资源缓存时，最好在 Expires 字段内写入与首部字段 Date 相同的时间值。

# 确保安全的 HTTPS

## HTTP 的缺点

### 通信使用明文（不加密），内容可能会被窃听
TCP/IP 是可能被窃听的网络，按 TCP/IP 协议族的工作机制，通信内容在所有的通信线路上都有可能遭到窥视。

### 不验证通信方的身份，因此有可能遭遇伪装
HTTP 协议中的请求和响应不会对通信方进行确认。也就是说存在“服务器是否就是发送请求中 URI 真正指定的主机，返回的响应是否真的返回到实际提出请求的客户端”等类似问题。

### 无法证明报文的完整性，所以有可能已遭篡改
比如，从某个 Web 网站上下载内容，是无法确定客户端下载的文件和服务器上存放的文件是否前后一致的。文件内容在传输途中可能已经被篡改为其他的内容。即使内容真的已改变，作为接收方的客户端也是觉察不到的。
像这样，请求或响应在传输途中，遭攻击者拦截并篡改内容的攻击称为**中间人攻击**

虽然有使用 HTTP 协议确定报文完整性的方法，但事实上并不便捷、可靠。其中常用的是 **MD5** 和 **SHA-1** 等散列值校验的方法，以及用来确认文件的数字签名方法。

## HTTP+ 加密 + 认证 + 完整性保护 = HTTPS
HTTPS 并非是应用层的一种新协议。只是 HTTP 通信接口部分用 SSL（Secure Socket Layer）和 TLS（Transport Layer Security）协议代替而已。

### 相互交换密钥的公开密钥加密技术
公开密钥加密方式很好地解决了共享密钥加密的困难。
公开密钥加密使用一对非对称的密钥。一把叫做私有密钥（private key），另一把叫做公开密钥（public key）。顾名思义，私有密钥不能让其他任何人知道，而公开密钥则可以随意发布，任何人都可以获得。

使用公开密钥加密方式，发送密文的一方使用对方的公开密钥进行加密处理，对方收到被加密的信息后，再使用自己的私有密钥进行解密。利用这种方式，不需要发送用来解密的私有密钥，也不必担心密钥被攻击者窃听而盗走。

### HTTPS 采用混合加密机制
1. 使用公开密钥加密方式安全地交换在稍后的共享密钥加密中要使用的密钥
1. 确保交换的密钥是安全的前提下，使用共享密钥加密方式进行通信

### 证明公开密钥正确性的证书
为了解决公开密钥加密方式无法证明公开密钥本身就是货真价实的公开密钥的问题，可以使用由数字证书认证机构（CA，Certificate Authority）和其相关机关颁发的公开密钥证书。

首先，服务器的运营人员向数字证书认证机构提出公开密钥的申请。数字证书认证机构在判明提出申请者的身份之后，会对已申请的公开密钥做数字签名，然后分配这个已签名的公开密钥，并将该公开密钥放入公钥证书后绑定在一起。

服务器会将这份由数字证书认证机构颁发的公钥证书发送给客户端，以进行公开密钥加密方式通信。公钥证书也可叫做数字证书或直接称为证书。

接到证书的客户端可使用数字证书认证机构的公开密钥，对那张证书上的数字签名进行验证，一旦验证通过，客户端便可明确两件事：一，认证服务器的公开密钥的是真实有效的数字证书认证机构。二，服务器的公开密钥是值得信赖的。

此处认证机关的公开密钥必须安全地转交给客户端。使用通信方式时，如何安全转交是一件很困难的事，因此，多数浏览器开发商发布版本时，会事先在内部植入常用认证机关的公开密钥。

### 可证明组织真实性的 EV SSL 证书
证书的一个作用是用来证明作为通信一方的服务器是否规范，另外一个作用是可确认对方服务器背后运营的企业是否真实存在。拥有该特性的证书就是 EV SSL 证书（Extended Validation SSL Certificate）。

持有 EV SSL 证书的 Web 网站的浏览器地址栏处的背景色是绿色的，从视觉上就能一眼辨别出。而且在地址栏的左侧显示了 SSL 证书中记录的组织名称以及颁发证书的认证机构的名称。

### HTTPS通信步骤

![](https://gitee.com/cellophane/image/raw/master/20200507213447.png)

1. 客户端通过发送 **Client Hello** 报文开始 SSL 通信。这个报文里包含了一个客户端生成的随机数 **Random1**、客户端支持的加密套件（Support Ciphers）和 SSL Version 等信息。
1. 服务器可进行 SSL 通信时，会以 **Server Hello** 报文作为应答。这个消息会从 Client Hello 传过来的 Support Ciphers 里确定一份加密套件，这个套件决定了后续加密和生成摘要时具体使用哪些算法，另外还会生成一份随机数 **Random2**。注意，至此客户端和服务端都拥有了两个随机数（Random1+ Random2），这两个随机数会在后续生成对称秘钥时用到。
1. 之后服务器发送 **Certificate** 报文，让客户端验证自己的身份，客户端验证通过后取出证书中的公钥。
1. 最后服务器发送 **Server Hello Done** 报文通知客户端，最初阶段的 SSL 握手协商部分结束。
1. SSL 第一次握手结束之后，客户端以 Client Key Exchange 报文作为回应。报文中包含通信加密中使用的一种被称为 **Pre-master secret** 的随机密码串，服务端在用自己的私钥解出这个  **Pre-master secret** 得到客户端生成的 **Random3**，两边再根据同样的算法就可以生成一份秘钥，握手结束后的应用层数据都是使用这个秘钥进行对称加密。
1. 接着客户端继续发送 **Change Cipher Spec** 报文。该报文会提示服务器，在此报文之后的通信会采用前面协商出来的密钥加密。
1. 客户端发送 **Encrypted Handshake Message(Client)** 报文。客户端将前面的握手消息生成摘要再用协商好的秘钥加密，这是客户端发出的第一条加密消息。服务端接收后会用秘钥解密，能解出来说明前面协商出来的秘钥是一致的。
1. 服务器同样发送 **Change Cipher Spec** 报文。
1. 服务器同样发送 **Encrypted Handshake Message(Server)** 报文。

在以上流程中，应用层发送数据时会附加一种叫做 MAC（Message Authentication Code）的报文摘要。MAC 能够查知报文是否遭到篡改，从而保护报文的完整性。

### SSL 和 TLS
IETF 以 SSL3.0 为基准，后又制定了 TLS1.0、TLS1.1 和 TLS1.2。TSL 是以 SSL 为原型开发的协议，有时会统一称该协议为 SSL。当前主流的版本是 SSL3.0 和 TLS1.0。

# 确认访问用户身份的认证

## 何为认证
- 密码：只有本人才会知道的字符串信息。
- 动态令牌：仅限本人持有的设备内显示的一次性密码。
- 数字证书：仅限本人（终端）持有的信息。
- 生物认证：指纹和虹膜等本人的生理信息。
- IC 卡等：仅限本人持有的信息。

## BASIC 认证
1. 当请求的资源需要 BASIC 认证时，服务器会随状态码 401 Authorization Required，返回带 WWW-Authenticate 首部字段的响应。该字段内包含认证的方式（BASIC） 及 Request-URI 安全域字符串（realm）。
1. 接收到状态码 401 的客户端为了通过 BASIC 认证，需要将用户 ID 及密码发送给服务器。发送的字符串内容是由用户 ID 和密码构成，两者中间以冒号（:）连接后，再经过 Base64 编码处理。假设用户 ID 为 guest，密码是 guest，连接起来就会形成 guest:guest 这样的字符串。然后经过 Base64 编码，最后的结果即是 Z3Vlc3Q6Z3Vlc3Q=。把这串字符串写入首部字段 Authorization 后，发送请求。当用户代理为浏览器时，用户仅需输入用户 ID 和密码即可，之后，浏览器会自动完成到 Base64 编码的转换工作。
1. 接收到包含首部字段 Authorization 请求的服务器，会对认证信息的正确性进行验证。如验证通过，则返回一条包含 Request-URI 资源的响应。

BASIC 认证虽然采用 Base64 编码方式，但这不是加密处理。不需要任何附加信息即可对其解码。换言之，由于明文解码后就是用户 ID 和密码，在 HTTP 等非加密通信的线路上进行 BASIC 认证的过程中，如果被人窃听，被盗的可能性极高。

另外，除此之外想再进行一次 BASIC 认证时，一般的浏览器却无法实现认证注销操作，这也是问题之一。

BASIC 认证使用上不够便捷灵活，且达不到多数 Web 网站期望的安全性等级，因此它并不常用。

## DIGEST 认证
1. 请求需认证的资源时，服务器会随着状态码 401 Authorization Required，返回带 WWW-Authenticate 首部字段的响应。该字段内包含质问响应方式认证所需的临时质询码（随机数，nonce）。

    首部字段 WWW-Authenticate 内必须包含 realm 和 nonce 这两个字段的信息。客户端就是依靠向服务器回送这两个值进行认证的。

    nonce 是一种每次随返回的 401 响应生成的任意随机字符串。该字符串通常推荐由 Base64 编码的十六进制数的组成形式，但实际内容依赖服务器的具体实现。
1. 接收到 401 状态码的客户端，返回的响应中包含 DIGEST 认证必须的首部字段 Authorization 信息。

    首部字段 Authorization 内必须包含 username、realm、nonce、uri 和 response 的字段信息。其中，realm 和 nonce 就是之前从服务器接收到的响应中的字段。

    username 是 realm 限定范围内可进行认证的用户名。

    uri（digest-uri）即 Request-URI 的值，但考虑到经代理转发后 Request-URI 的值可能被修改，因此事先会复制一份副本保存在 uri 内。

    response 也可叫做 Request-Digest，存放经过 MD5 运算后的密码字符串，形成响应码。
1. 接收到包含首部字段 Authorization 请求的服务器，会确认认证信息的正确性。认证通过后则返回包含 Request-URI 资源的响应。并且这时会在首部字段 Authentication-Info 写入一些认证成功的相关信息。

DIGEST 认证提供了高于 BASIC 认证的安全等级，但是和 HTTPS 的客户端认证相比仍旧很弱。DIGEST 认证提供防止密码被窃听的保护机制，但并不存在防止用户伪装的保护机制。

DIGEST 认证和 BASIC 认证一样，使用上不那么便捷灵活，且仍达不到多数 Web 网站对高度安全等级的追求标准。因此它的适用范围也有所受限。

## SSL 客户端认证
为达到 SSL 客户端认证的目的，需要事先将客户端证书分发给客户端，且客户端必须安装此证书。
1. 接收到需要认证资源的请求，服务器会发送 Certificate Request 报文，要求客户端提供客户端证书。
1. 用户选择发送的客户端证书后，客户端会把客户端证书信息以 Client Certificate 报文方式发送给服务器。
1. 服务器验证客户端证书验证通过后方可领取证书内客户端的公开密钥，然后开始 HTTPS 加密通信。

## 基于表单认证
1. 客户端把用户 ID 和密码等登录信息放入报文的实体部分，通常是以 POST 方法把请求发送给服务器。而这时，会使用 HTTPS 通信来进行 HTML 表单画面的显示和用户输入数据的发送。
1. 服务器会发放用以识别用户的 Session ID。通过验证从客户端发送过来的登录信息进行身份认证，然后把用户的认证状态与 Session ID 绑定后记录在服务器端。向客户端返回响应时，会在首部字段 Set-Cookie 内写入 Session ID（如 PHPSESSID=028a8c…）。

    然而，如果 Session ID 被第三方盗走，对方就可以伪装成你的身份进行恶意操作了。因此必须防止 Session ID 被盗，或被猜出。为了做到这点，Session ID 应使用难以推测的字符串，且服务器端也需要进行有效期的管理，保证其安全性。

    另外，为减轻跨站脚本攻击（XSS）造成的损失，建议事先在 Cookie 内加上 httponly 属性。

1. 客户端接收到从服务器端发来的 Session ID 后，会将其作为 Cookie 保存在本地。下次向服务器发送请求时，浏览器会自动发送 Cookie，所以 Session ID 也随之发送到服务器。服务器端可通过验证接收到的 Session ID 识别用户和其认证状态。

除了以上介绍的应用实例，还有应用其他不同方法的案例。

另外，不仅基于表单认证的登录信息及认证过程都无标准化的方法，服务器端应如何保存用户提交的密码等登录信息等也没有标准化。

通常，一种安全的保存方法是，先利用给密码加盐（salt）的方式增加额外信息，再使用散列（hash）函数计算出散列值后保存。但是我们也经常看到直接保存明文密码的做法，而这样的做法具有导致密码泄露的风险。

# 第九章

## HTTP 的瓶颈
- 一条连接上只可发送一个请求
- 请求只能从客户端开始，客户端不可以接收除响应以外的指令
- 请求/响应首部未经压缩就发送。首部信息越多延迟越大
- 发送冗长的首部。每次互相发送相同的首部造成的浪费较大
- 可任意选择数据压缩格式。非强制压缩发送

## HTTP/2.0
- 多路复用流：通过单一的 TCP 连接，可以无限制处理多个 HTTP 请求。
- 赋予请求优先级
- 压缩 HTTP 首部：将原来每次要携带的大量 key value 在两端建立一个索引表，对相同的头之发动索引表中的索引
- 二进制分帧：将阐述信息分割为更小的消息和帧，对他们采用二进制格式编码。比如 header 帧，Data 帧。这些帧可以打散乱序发送， 然后根据每个帧首部的流标识符重新组装，并且可以根据优先级，决定优先处理哪个流的数据。解决了队头阻塞问题。
- 服务器推送功能：服务器可以主动推送客户端请求所需资源。由于在客户端发现资源之前就可以获知资源的存在，因此在资源已缓存等情况下，可以避免发送不必要的请求。

### 队头阻塞
HTTP 管道化要求服务端必须按照请求发送的顺序返回响应，那如果一个响应返回延迟了，那么其后续的响应都会被延迟，直到队头的响应送达。

## WebSocket
WebSocket，即 Web 浏览器与 Web 服务器之间全双工通信标准。

一旦 Web 服务器与客户端之间建立起 WebSocket 协议的通信连接，之后所有的通信都依靠这个专用协议进行。通信过程中可互相发送 JSON、XML、HTML 或图片等任意格式的数据。

主要特点：
- 推送功能
- 减少通信量

为了实现 WebSocket 通信，在 HTTP 连接建立之后，需要完成一次“握手”的步骤
- 握手·请求：为了实现 WebSocket 通信，需要用到 HTTP 的 Upgrade 首部字段，告知服务器通信协议发生改变，以达到握手的目的。
- 握手·响应：对于之前的请求，返回状态码 101 Switching Protocols 的响应。

成功握手确立 WebSocket 连接之后，通信时不再使用 HTTP 的数据帧，而采用 WebSocket 独立的数据帧。

# 第十一章 Web 的攻击技术

## 主动攻击
主动攻击（active attack）是指攻击者通过直接访问 Web 应用，把攻击代码传入的攻击模式。由于该模式是直接针对服务器上的资源进行攻击，因此攻击者需要能够访问到那些资源。

主动攻击模式里具有代表性的攻击是 SQL 注入攻击和 OS 命令注入攻击。

## 被动攻击
被动攻击（passive attack）是指利用圈套策略执行攻击代码的攻击模式。在被动攻击过程中，攻击者不直接对目标 Web 应用访问发起攻击。
被动攻击通常的攻击模式如下所示。
1. 攻击者诱使用户触发已设置好的陷阱，而陷阱会启动发送已嵌入攻击代码的 HTTP 请求。
1. 当用户不知不觉中招之后，用户的浏览器或邮件客户端就会触发这个陷阱。
1. 中招后的用户浏览器会把含有攻击代码的 HTTP 请求发送给作为攻击目标的 Web 应用，运行攻击代码。
1. 执行完攻击代码，存在安全漏洞的 Web 应用会成为攻击者的跳板，可能导致用户所持的 Cookie 等个人信息被窃取，登录状态中的用户权限遭恶意滥用等后果。

被动攻击模式中具有代表性的攻击是跨站脚本攻击和跨站点请求伪造。

## 因输出值转义不完全引发的安全漏洞
- 跨站脚本攻击（XSS）
    跨站脚本攻击（Cross-Site Scripting，XSS）是指通过存在安全漏洞的 Web 网站注册用户的浏览器内运行非法的 HTML 标签或 JavaScript 进行的一种攻击。动态创建的 HTML 部分有可能隐藏着安全漏洞。就这样，攻击者编写脚本设下陷阱，用户在自己的浏览器上运行时，一不小心就会受到被动攻击。
    - 利用虚假输入表单骗取用户个人信息
    - 利用脚本窃取用户的 Cookie 值，被害者在不知情的情况下，帮助攻击者发送恶意请求
    - 显示伪造的文章或图片
- SQL 注入攻击
- OS 命令注入攻击
- HTTP 首部注入攻击
- 邮箱首部注入攻击
- 目录遍历攻击

## 因设置或设计上的缺陷引发的安全漏洞
- 强制浏览
- 不正确的错误消息处理
- 开放重定向

## 因会话管理疏忽引发的安全漏洞
- 会话劫持
- 会话固定攻击
- 跨站点请求伪造（CSRF）

## 其他安全漏洞
- 密码破解
- 点击劫持
- DoS 攻击

# 参考文章

- 图解 HTTP 
- [全网最透彻HTTPS（面试常问）](https://mp.weixin.qq.com/s?__biz=MzAwNDA2OTM1Ng==&mid=2453141883&idx=2&sn=3b93d3bed05ec0094a0cae77bf1cc82c&chksm=8cf2dbf8bb8552ee286c4799b30d3847a641760142e50234b12bd042f9d238b3291c161b5996&scene=0&xtrack=1#rd)
- [燕十八-HTTP 视频教程](https://www.bilibili.com/video/BV1At41137Q8/?spm_id_from=333.788.videocard.5)