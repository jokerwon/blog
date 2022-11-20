---
title: 《图解HTTP》笔记
tag: [HTTP, Web]
---

***本文是对《图解HTTP》一书的摘抄与总结***

<!-- more -->

## 一、了解 Web 及网络基础

### 1.3 网络基础 TCP/IP

#### 1.3.1 TCP/IP 协议族

把与互联网相关联的协议集合起来总称为TCP/IP。

#### 1.3.2 TCP/IP 的分层管理

##### 应用层

应用层决定了向用户提供应用服务时通信的活动。

FTP、DNS、HTTP

##### 传输层

传输层提供处于网络连接中的两台计算机之间的数据传输。

TCP、UDP

##### 网络层（网络互连层）

网络层用来处理在网络上流动的数据包。**数据包是网络传输的最小数据单位。**

> 与对方计算机之间通过多台计算机或网络设备进行传输时，网络层所起的作用就是在众多的选项内选择一条传输路线。

##### 链路层（又名数据链路层，网络接口层）

用来处理连接网络的硬件部分。包括控制操作系统、硬件的设备驱动、NIC（Network Interface Card，网络适配器，即网卡），及光纤等物理可见部分（还包括连接器等一切传输媒介）。硬件上的范畴均在链路层的作用范围之内。

#### 1.3.3 TCP/IP 通信传输流

![流程图](https://cdn.jsdelivr.net/gh/jokerwon/images/img/202206171629535.png)

### 1.5 负责域名解析的 DNS 服务

DNS协议提供通过域名查找IP地址，或逆向从IP地址反查域名的服务。

### 1.6 各种协议与 HTTP 协议的关系

![](https://cdn.jsdelivr.net/gh/jokerwon/images/img/202206171636650.png)





## 六、HTTP 首部

### 6.1 HTTP 报文首部

### 6.2 HTTP 首部字段

### 6.3 HTTP/1.1 通用首部字段

### 6.4 请求首部字段

请求首部字段是从客户端往服务器端发送请求报文中所使用的字段，用于补充请求的附加信息、客户端信息、对响应内容相关的优先级等内容。

#### 6.4.1 Accept

Accept 首部字段可通知服务器，用户代理能够处理的媒体类型及媒体类型的相对优先级。可使用 type/subtype 这种形式，一次指定多种媒体类型。

若想要给显示的媒体类型增加优先级，则使用q=来额外表示权重值[插图]，用分号（;）进行分隔。权重值q的范围是0～1（可精确到小数点后3位），且1为最大值。**不指定权重q值时，默认权重为q=1.0**。

~~~http
// 表示 application/xml 的权值为 0.9
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
~~~

常用 MIME 类型：

- 文本文件
  - text/html, text/plain, text/css, ...
  - application/xhtml+xml, application/xml, ...
- 图片文件
  - image/jpeg, image/gif, image/png, ...
- 视频文件
  - video/mpeg, video/quicktime, ...
- 应用程序使用的二进制文件
  - application/octet-stream, application/zip, ...

#### 6.4.2 Accept-Charset

Accept-Charset 首部字段可用来通知服务器用户代理支持的字符集及字符集的相对优先顺序。另外，可一次性指定多种字符集。与首部字段Accept相同的是可用权重q值来表示相对优先级。

~~~
Accept-Charset: iso-8859-5, unicode-1-1;q=0.8
~~~

#### 6.4.3 Accept-Encoding

Accept-Encoding 首部字段用来告知服务器用户代理支持的内容编码及内容编码的优先级顺序。可一次性指定多种内容编码。采用权重q值来表示相对优先级，这点与首部字段Accept相同。另外，也可使用星号（*）作为通配符，指定任意的编码格式。

~~~
Accept-Encoding: gzip, deflate
~~~

常见的内容编码：

- gzip

  由文件压缩程序gzip（GNU zip）生成的编码格式（RFC1952），采用Lempel-Ziv算法（LZ77）及32位循环冗余校验（Cyclic Redundancy Check，通称CRC）。

- compress

  由UNIX文件压缩程序compress生成的编码格式，采用Lempel-Ziv-Welch算法（LZW）。

- deflate

  组合使用zlib格式（RFC1950）及由deflate压缩算法（RFC1951）生成的编码格式。

- identity

  不执行压缩或不会变化的默认编码格式

#### 6.4.4 Accept-Language

Accept-Language 首部字段用来告知服务器用户代理能够处理的自然语言集（指中文或英文等），以及自然语言集的相对优先级。可一次指定多种自然语言集。和Accept首部字段一样，按权重值q来表示相对优先级。

~~~
Accept-Language: zh-cn, zh;q=0.7, en-us, en;q=0.3
~~~

#### 6.4.5 Authorization

Authorization 首部字段是用来告知服务器，用户代理的认证信息（证书值）。通常，想要通过服务器认证的用户代理会在接收到返回的401状态码响应后，把首部字段Authorization加入请求中。共用缓存在接收到含有Authorization首部字段的请求时的操作处理会略有差异。

~~~
Authorization: Basic dWVub3N1bjpwaeDJHOa243A==
~~~

#### 6.4.6 Expect

Expect 首部字段来告知服务器，期望出现的某种特定行为。因服务器无法理解客户端的期望作出回应而发生错误时，会返回状态码417 Expectation Failed。

客户端可以利用该首部字段，写明所期望的扩展。虽然HTTP/1.1规范只定义了100-continue（状态码100 Continue之意）。等待状态码100响应的客户端在发生请求时，需要指定Expect:100-continue。

~~~
Expect: 100-continue
~~~

#### 6.4.7 From

From 首部字段用来告知服务器使用用户代理的用户的电子邮件地址。通常，其使用目的就是为了显示搜索引擎等用户代理的负责人的电子邮件联系方式。使用代理时，应尽可能包含From首部字段（但可能会因代理不同，将电子邮件地址记录在User-Agent首部字段内）。

~~~
From: hello@email.com
~~~

#### 6.4.8 Host

Host 首部字段会告知服务器，请求的资源所处的互联网主机名和端口号。**Host首部字段在HTTP/1.1规范内是唯一一个必须被包含在请求内的首部字段。**

首部字段Host和以单台服务器分配多个域名的虚拟主机的工作机制有很密切的关联，这是首部字段Host必须存在的意义。

请求被发送至服务器时，请求中的主机名会用IP地址直接替换解决。但如果这时，相同的IP地址下部署运行着多个域名，那么服务器就会无法理解究竟是哪个域名对应的请求。因此，就需要使用首部字段Host来明确指出请求的主机名。若服务器未设定主机名，那直接发送一个空值即可。

#### 6.4.9 If-Match

*形如If-xxx这种样式的请求首部字段，都可称为条件请求。服务器接收到附带条件的请求后，只有判断指定条件为真时，才会执行请求。*

If-Match 首部字段，属附带条件之一，它会告知服务器匹配资源所用的实体标记（ETag）值。这时的服务器无法使用弱ETag值。

服务器会比对If-Match的字段值和资源的ETag值，仅当两者一致时，才会执行请求。反之，则返回状态码412 Precondition Failed的响应。还可以使用星号（*）指定If-Match的字段值。针对这种情况，服务器将会忽略ETag的值，只要资源存在就处理请求。

#### 6.4.10 If-Modified-Since

If-Modified-Since 首部字段属于附带条件之一，它会告知服务器若资源在 If-Modified-Since 字段值之后更新过资源，则处理该请求。而在指定 If-Modified-Since字段值的日期时间之后，如果请求的资源都没有过更新，则返回状态码 304 Not Modified 的响应。

If-Modified-Since 用于确认代理或客户端拥有的本地资源的有效性。获取资源的更新日期时间，可通过确认响应首部字段 Last-Modified 来确定。

~~~
If-Modified-Since: Thu, 15 May 2020 00:00:00 GMT
~~~

#### 6.4.11 If-None-Match

If-None-Match 首部字段属于附带条件之一。它和首部字段 If-Match 作用相反。用于指定 If-None-Match 字段值的实体标记（ETag）值与请求资源的 ETag 不一致时，它就告知服务器处理该请求。

在 `GET` 或 `HEAD` 方法中使用首部字段 If-None-Match 可获取最新的资源。因此，这与使用首部字段 If-Modified-Since 时有些类似。

~~~http
PUT /sample.html
If-None-Match: *
~~~

#### 6.4.12 If-Range

If-Range 首部字段属于附带条件之一。它告知服务器若指定的 If-Range 字段值（ETag值或者时间）和请求资源的   ETag 值或时间相一致时，则作为范围请求处理。反之，则返回全体资源。

~~~http
GET /index.html
If-Range: "123456"
Range: bytes=5001-10000

// If-Range 字段值若与 ETag 或 更新日期时间匹配一致，则作为范围请求处理
206 Partial Content
Content-Range: bytes 5001-10000/10000
Content-Length: 5000

// 若不一致，则忽略范围请求，返回全部资源
200 OK
ETag: "567890"
~~~

#### 6.4.13 If-Unmodified-Since

If-Unmodified-Since 首部字段和 If-Modified-Since 首部字段的作用相反。它的作用的是告知服务器，指定的请求资源只有在字段值内指定的日期时间之后，未发生更新的情况下，才能处理请求。如果在指定日期时间后发生了更新，则以状态码 412 Precondition Failed 作为响应返回。

~~~
If-Unmodified-Since: Thu, 03 May 2020 00:00:00 GMT
~~~

#### 6.4.14 Max-Forwards

通过 `TRACE` 方法或 `OPTIONS` 方法，发送包含首部字段 Max-Forwards 的请求时，该字段以十进制整数形式指定可经过的服务器最大数目。服务器在往下一个服务器转发请求之前，会将 Max-Forwards 的值减1后重新赋值。当服务器接收到 Max-Forwards 值为 0 的请求时，则不再进行转发，而是直接返回响应。

使用 HTTP协议通信时，请求可能会经过代理等多台服务器。途中，如果代理服务器由于某些原因导致请求转发失败，客户端也就等不到服务器返回的响应了。对此，我们无从可知。可以灵活使用首部字段 Max-Forwards，针对以上问题产生的原因展开调查。由于当 Max-Forwards 字段值为0时，服务器就会立即返回响应，由此我们至少可以对以那台服务器为终点的传输路径的通信状况有所把握。

~~~
Max-Forwards: 10
~~~

#### 6.4.15 Proxy-Authorization

接收到从代理服务器发来的认证质询时，客户端会发送包含首部字段 Proxy-Authorization 的请求，以告知服务器认证所需要的信息。

~~~
Proxy-Authorization: Basic sDa4mSUYJ89dfY5
~~~

这个行为是与客户端和服务器之间的HTTP访问认证相类似的，不同之处在于，认证行为发生在客户端与代理之间。客户端与服务器之间的认证，使用首部字段Authorization可起到相同作用。

#### 6.4.16 Range

Range 首部字段告知服务器资源的指定范围，用于只需获取部分资源的范围请求。

~~~
// 获取从第5001字节至第10000字节的资源
Range: bytes=5001-10000
~~~

接收到附带 Range 首部字段请求的服务器，会在处理请求之后返回状态码为 206 PartialContent 的响应。无法处理该范围请求时，则会返回状态码 200 OK 的响应及全部资源。

#### 6.4.17 Referer

Referer 首部字段会告知服务器请求的原始资源的URI。

~~~
Referer: http://www.baidu.com/index.htm
~~~

客户端一般都会发送 Referer 首部字段给服务器。但当直接在浏览器的地址栏输入URI，或出于安全性的考虑时，也可以不发送该首部字段。

#### 6.4.18 TE

TE 首部字段会告知服务器客户端能够处理响应的传输编码方式及相对优先级。它和首部字段Accept-Encoding的功能很相像，但是用于传输编码。

~~~
TE: gzip, delate;q=0.5
~~~

首部字段TE除指定传输编码之外，还可以指定伴随trailer字段的分块传输编码的方式。应用后者时，只需把trailers赋值给该字段值。

~~~
TE: trailers
~~~

#### 6.4.19 User-Agent

User-Agent 首部字段会将创建请求的浏览器和用户代理名称等信息传达给服务器。

由网络爬虫发起请求时，有可能会在字段内添加爬虫作者的电子邮件地址。此外，如果请求经过代理，那么中间也很可能被添加上代理服务器的名称。

~~~
User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36
~~~

### 6.5 响应首部字段

响应首部字段是由服务器端向客户端返回响应报文中所使用的字段，用于补充响应的附加信息、服务器信息，以及对客户端的附加要求等信息。

#### 6.5.1 Accept-Ranges

首部字段 Accept-Ranges 是用来告知客户端服务器是否能处理范围请求，以指定获取服务器端某个部分的资源。

可指定的字段值有两种，可处理范围请求时指定其为bytes，反之则指定其为 none。

~~~
Accept-Ranges: bytes
~~~

#### 6.5.2 Age

首部字段 Age 能告知客户端，源服务器在多久前创建了响应以及消息对象在缓存代理服务器中存贮的时长。**字段值的单位为秒**。

若创建该响应的服务器是缓存服务器，Age 值是指缓存后的响应再次发起认证到认证完成的时间值。代理创建响应时必须加上首部字段Age。

~~~
Age: 600
~~~

#### 6.5.3 ETag

首部字段 ETag 能告知客户端实体标识。它是一种可将资源以字符串形式做唯一性标识的方式。服务器会为每份资源分配对应的 ETag 值。当资源更新时，ETag 值也需要更新。

~~~
ETag: "5eba265e-57256"
~~~

**ETag中有强ETag值和弱ETag值之分。**

- **强 ETag**

  强ETag值，不论实体发生多么细微的变化都会改变其值。

  ~~~
  ETag: "5eba265e-57256"
  ~~~

- **弱 Etag**

  弱ETag值只用于提示资源是否相同。只有资源发生了根本改变，产生差异时才会改变 ETag 值。这时，会在字段值最开始处附加 `W/`。

  ~~~
  Etag: W/"5eba265e-57256"
  ~~~

  值得一提的是，在我进行web优化时，曾试图开启 nginx 的 gzip_static 优化。如果本地存在源文件的 .gz 格式的压缩包，就将其返回，不会再占用 CPU 资源将源文件进行压缩。判断 nginx 是否返回了主动提供的 .gz 文件比较多的说法，就是判断响应头中 ETag 的强弱，如果返回的是 **强 ETag**，则代表 gzip_static 配置生效，否则返回了 nginx 压缩的文件。

#### 6.5.4 Location

使用首部字段 Location 可以将响应接收方引导至某个与请求URI位置不同的资源。

基本上，该字段会配合3xx:Redirection的响应，提供重定向的URI。几乎所有的浏览器在接收到包含首部字段Location的响应后，都会强制性地尝试对已提示的重定向资源的访问。

~~~
Location: http://www.tencent.com/index.html
~~~

#### 6.5.5 Proxy-Authenticate

首部字段 Proxy-Authenticate 会把由代理服务器所要求的认证信息发送给客户端。

它与客户端和服务器之间的 HTTP 访问认证的行为相似，不同之处在于其认证行为是在客户端与代理之间进行的。而客户端与服务器之间进行认证时，首部字段 WWW-Authorization 有着相同的作用。

~~~
Proxy-Authenticate: Basic realm="Access to the internal site"
~~~

#### 6.5.6 Retry-After

首部字段 Retry-After 告知客户端应该在多久之后再次发送请求。要应用于以下两种场景：

- 当与 [`503`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/503) (Service Unavailable，当前服务不存在) 响应一起发送的时候，表示服务下线的预期时长。
- 当与 3xx 重定向响应一起发送的时候，比如 [`301`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/301) (Moved Permanently，永久迁移)，表示用户代理在发送重定向请求之前需要等待的最短时间。

字段值可以指定为具体的日期时间（Wed, 04 Jul 2012 06:34:24 GMT等格式），也可以是创建响应后的秒数。

~~~
Retry-After: Wed, 21 Oct 2015 07:28:00 GMT  // 表示此时间后可以重新尝试
Retry-After: 120  // 表示重试之前需要等待的秒数
~~~

#### 6.5.7 Server

首部字段 Server 告知客户端当前服务器上安装的HTTP服务器应用程序的信息。不单单会标出服务器上的软件应用名称，还有可能包括版本号和安装时启用的可选项。

应该避免使用过长或者过于详细的描述作为 Server 的值，因为这有可能泄露服务器的内部实现细节，有利于攻击者找到或者探测已知的安全漏洞。

~~~
Server: Apache/2.4.1 (Unix)
Server: nginx/1.15.10
~~~

#### 6.5.8 Vary

首部字段 Vary 可对缓存进行控制。源服务器会向代理服务器传达关于本地缓存使用方法的命令。

参照下面的一次请求：

~~~
// 客户端
GET /sample.html
Accept-Language: en-us

 👇👇👇

// 代理服务器
GET /sample.html
Accept-Language: en-us

 👇👇👇
 
// 源服务器
...
Vary: Accept-Language
~~~

源服务器向代理服务器返回的响应首部中含有值为  Accept-Language 的 Vary 字段。当代理服务器接收到带有 Vary 首部字段指定获取资源的请求时，如果使用的 Accept-Language 字段的值相同，那么就直接从缓存返回响应。反之，则需要先从源服务器端获取资源后才能作为响应返回。

#### 6.5.9 WWW-Authenticate

首部字段 WWW-Authenticate 用于HTTP访问认证。它会告知客户端适用于访问请求URI所指定资源的认证方案（Basic或是Digest）和带参数提示的质询（challenge）。状态码 401Unauthorized 响应中，肯定带有首部字段WWW-Authenticate。

~~~
WWW-Authenticate: Basic realm="Access to the staging site"
~~~

### 6.6 实体首部字段

实体首部字段是包含在请求报文和响应报文中的实体部分所使用的首部，用于补充内容的更新时间等与实体相关的信息。其位置可能分布在请求首部或响应首部中。

#### 6.6.1 Allow

首部字段 Allow 用于通知客户端能够支持Request-URI指定资源的所有HTTP方法。当服务器接收到不支持的 HTTP 方法时，会以状态码 405 Method Not Allowed 作为响应返回。与此同时，还会把所有能支持的 HTTP 方法写入首部字段 Allow 后返回。

~~~
Allow: GET, HEAD
~~~

#### 6.6.2 Content-Encoding

首部字段 Content-Encoding 会告知客户端服务器对实体的主体部分选用的内容编码方式。内容编码是指在不丢失实体信息的前提下所进行的压缩。

~~~
Content-Encoding: gzip
~~~

常用的编码方式：

- gzip
- compress
- deflate
- identity

#### 6.6.3 Content-Language

首部字段 Content-Language 会告知客户端，实体主体使用的自然语言（指中文或英文等语言）。

~~~
Content-Language: zh-CN
~~~

#### 6.6.4 Content-Length

首部字段 Content-Length 表明了实体主体部分的大小（单位是字节）。对实体主体进行内容编码传输时，不能再使用 Content-Length 首部字段。但是我在使用 gzip 进行传输的时候，响应中还是携带了保存了准确的文件大小的 Content-Length。

~~~
Content-Length： 15000
~~~

#### 6.6.5 Content-Location

首部字段 Content-Location 给出与报文主体部分相对应的URI。和首部字段 Location 不同，Content-Location 表示的是报文主体返回资源对应的 URI。

比如，对于使用首部字段 Accept-Language 的服务器驱动型请求，当返回的页面内容与实际请求的对象不同时，首部字段 Content-Location 内会写明 URI。

~~~
Content-Location: http://www.netease.com/index.html
~~~

#### 6.6.6 Content-MD5

首部字段 Content-MD5 是一串由 MD5 算法生成的值，其目的在于检查报文主体在传输过程中是否保持完整，以及确认传输到达。

对报文主体执行 MD5 算法获得的 128 位二进制数，再通过 Base64 编码后将结果写入 Content-MD5 字段值。由于HTTP首部无法记录二进制值，所以要通过 Base64 编码处理。**为确保报文的有效性，作为接收方的客户端会对报文主体再执行一次相同的 MD5 算法。计算出的值与字段值作比较后，即可判断出报文主体的准确性。**

采用这种方法，对内容上的偶发性改变是无从查证的，也无法检测出恶意篡改。其中一个原因在于，**内容如果能够被篡改，那么同时意味着 Content-MD5也可 重新计算然后被篡改。**所以处在接收阶段的客户端是无法意识到报文主体以及首部字段Content-MD5是已经被篡改过的。

#### 6.6.7 Content-Range

**针对范围请求**，返回响应时使用的首部字段 Content-Range，能告知客户端作为响应返回的实体的哪个部分符合范围请求。**字段值以字节为单位**，表示当前发送部分及整个实体大小。

~~~
Content-Range: bytes 5001-10000/10000
~~~

#### 6.6.8 Content-Type

首部字段 Content-Type 说明了实体主体内对象的媒体类型。和首部字段 Accept 一样，字段值用 type/subtype 形式赋值。参数charset使用iso-8859-1或euc-jp等字符集进行赋值。

~~~
Content-Type: text/html;charset=UTF-8
~~~

#### 6.6.9 Expires

首部字段 Expires 会将资源失效的日期告知客户端。缓存服务器在接收到含有首部字段 Expires 的响应后，会以缓存来应答请求，在 Expires 字段值指定的时间之前，响应的副本会一直被保存。当超过指定的时间后，缓存服务器在请求发送过来时，会转向源服务器请求资源。

源服务器不希望缓存服务器对资源缓存时，最好在 Expires 字段内写入与首部字段 Date 相同的时间值。

但是，**当首部字段 Cache-Control 有指定 max-age 指令时，比起首部字段 Expires，会优先处理 max-age 指令。**

~~~
Expires: Wed, 15 May 2020 00:00:00 GMT
~~~

#### 6.6.10 Last-Modified

首部字段 Last-Modified 指明资源最终修改的时间。一般来说，这个值就是Request-URI指定资源被修改的时间。但类似使用CGI脚本进行动态数据处理时，该值有可能会变成数据最终修改时的时间。

~~~
Last-Modified: Wed, 15 May 2020 00:00:00 GMT
~~~

### 6.7 为Cookie服务的首部字段

#### 6.7.1 Set-Cookie

~~~
Set-Cookie: status=enable; expires=Wed, 15 May 2020 00:00:00 GMT; => path=/; domain=baidu.com
~~~

##### 1. expires 属性

Cookie 的 expires 属性指定浏览器可发送 Cookie 的有效期。

当省略 expires 属性时，其有效期仅限于维持浏览器会话（Session）时间段内。这通常限于浏览器应用程序被关闭之前。

另外，一旦 Cookie 从服务器端发送至客户端，服务器端就不存在可以显式删除 Cookie 的方法。但可通过覆盖已过期的 Cookie，实现对客户端 Cookie 的实质性删除操作。

##### 2. path 属性

Cookie 的 path 属性可用于限制指定 Cookie 的发送范围的文件目录。

##### 3. domain 属性

通过Cookie的domain属性指定的域名可做到与结尾匹配一致。比如，当指定 example.com 后，除  example.com 以外，www.example.com 或 www2.example.com 等都可以发送 Cookie。

##### 4. secure 属性

Cookie 的 secure 属性用于限制 Web 页面仅在 HTTPS 安全连接时，才可以发送 Cookie。

发送Cookie时，指定secure属性的方法如下所示。

~~~
Set-Cookie: $name=$value; secure
~~~

##### 5. HttpOnly 属性

Cookie 的 HttpOnly 属性是 Cookie 的扩展功能，它**使 JavaScript 脚本无法获得 Cookie**。其主要目的为 **防止跨站脚本攻击（Cross-site scripting, XSS）对 Cookie 的信息窃取**。

发送指定HttpOnly属性的Cookie的方法如下所示。

~~~
Set-Cookie: $name=$value; HttpOnly 
~~~

顺带一提，该扩展并非是为了防止 XSS 而开发的。

#### 6.7.2 Cookie

首部字段 Cookie 会告知服务器，当客户端想获得 HTTP 状态管理支持时，就会在请求中包含从服务器接收到的 Cookie。接收到多个 Cookie 时，同样可以以多个 Cookie 形式发送。

~~~
Cookie: status=enable
~~~

### 6.8 其他首部字段

#### 6.8.1 X-Frame-Options

首部字段X-Frame-Options属于HTTP响应首部，用于控制网站内容在其他Web网站的Frame标签内的显示问题。其主要目的是为了防止点击劫持（clickjacking）攻击。

有以下两个可指定的字段值：

- **DENY**： 拒绝
- **SAMEORIGIN**： 仅同源域名下的页面（Top-level-browsing-context）匹配时许可。

#### 6.8.2 X-XSS-Protection

首部字段X-XSS-Protection属于HTTP响应首部，它是针对跨站脚本攻击（XSS）的一种对策，用于控制浏览器XSS防护机制的开关。

可指定的字段值如下：

- 0 ：将XSS过滤设置成无效状态
- 1 ：将XSS过滤设置成有效状态

#### 6.8.3 DNT

首部字段DNT属于HTTP请求首部，其中DNT是Do Not Track的简称，意为拒绝个人信息被收集，是表示拒绝被精准广告追踪的一种方法。

可指定的字段值如下：

- 0 ：同意被追踪

- 1 ：拒绝被追踪

#### 6.8.4 P3P

首部字段P3P属于HTTP响应首部，通过利用P3P（The Platform for Privacy Preferences，在线隐私偏好平台）技术，可以让Web网站上的个人隐私变成一种仅供程序可理解的形式，以达到保护用户隐私的目的。

## 七、HTTPS

### 7.1 HTTP 的缺点

HTTP 主要有这些不足：

- **通信使用明文（不加密），内容可能会被窃听**

- **不验证通信方的身份，因此有可能遭遇伪装**
- **无法证明报文的完整性，所以有可能已遭篡改**

### 7.2 HTTP+加密+认证+完整性保护=HTTPS

#### 7.2.1 HTTP加上加密处理和认证以及完整性保护后即是HTTPS

#### 7.2.2 HTTPS是身披SSL外壳的HTTP

![HTTPS](F:\jokerwon\skill-stack\notes\images/HTTP-HTTPS.png)

HTTPS并非是应用层的一种新协议。只是HTTP通信接口部分用SSL（Secure Socket Layer）和TLS（Transport Layer Security）协议代替而已。

通常，HTTP 直接和 TCP 通信。**当使用SSL时，则演变成先和 SSL 通信，再由 SSL 和 TCP 通信了**。简言之，所谓HTTPS，其实就是身披 SSL 协议这层外壳的 HTTP。

在采用SSL后，HTTP就拥有了HTTPS的加密、证书和完整性保护这些功能。

#### 7.2.3 相互交换密钥的公开密钥加密技术

SSL采用一种叫做公开密钥加密（Public-key cryptography）的加密处理方式。

公开密钥加密使用一对非对称的密钥。一把叫做私有密钥（private key），另一把叫做公开密钥（public key）。顾名思义，私有密钥不能让其他任何人知道，而公开密钥则可以随意发布，任何人都可以获得。

使用公开密钥加密方式，发送密文的一方使用对方的公开密钥进行加密处理，对方收到被加密的信息后，再使用自己的私有密钥进行解密。利用这种方式，不需要发送用来解密的私有密钥，也不必担心密钥被攻击者窃听而盗走。

<p style="font-size: 22px;font-weight: 600;">HTTPS采用混合加密机制</p>

HTTPS采用共享密钥加密和公开密钥加密两者并用的混合加密机制。若密钥能够实现安全交换，那么有可能会考虑仅使用公开密钥加密来通信。但是公开密钥加密与共享密钥加密相比，其处理速度要慢。

所以应充分利用两者各自的优势，将多种方法组合起来用于通信。**在交换密钥环节使用公开密钥加密方式，之后的建立通信交换报文阶段则使用共享密钥加密方式。**

#### 7.2.5 HTTPS的安全通信机制

HTTPS的通信步骤:

![HTTPS](F:\jokerwon\skill-stack\notes\images/HTTPS.png)

- 步骤1： 客户端通过发送Client Hello报文开始SSL通信。报文中包含客户端支持的SSL的指定版本、加密组件（Cipher Suite）列表（所使用的加密算法及密钥长度等）。
- 步骤2： 服务器可进行SSL通信时，会以Server Hello报文作为应答。和客户端一样，在报文中包含SSL版本以及加密组件。服务器的加密组件内容是从接收到的客户端加密组件内筛选出来的。
- 步骤3： 之后服务器发送Certificate报文。报文中包含公开密钥证书。
- 步骤4： 最后服务器发送Server Hello Done报文通知客户端，最初阶段的SSL握手协商部分结束。
- 步骤5: SSL第一次握手结束之后，客户端以Client Key Exchange报文作为回应。报文中包含通信加密中使用的一种被称为Pre-master secret的随机密码串。该报文已用步骤3中的公开密钥进行加密。
- 步骤6： 接着客户端继续发送Change Cipher Spec报文。该报文会提示服务器，在此报文之后的通信会采用Pre-master secret密钥加密。
- 步骤7： 客户端发送Finished报文。该报文包含连接至今全部报文的整体校验值。这次握手协商是否能够成功，要以服务器是否能够正确解密该报文作为判定标准。
- 步骤8： 服务器同样发送Change Cipher Spec报文。
- 步骤9： 服务器同样发送Finished报文。
- 步骤10： 服务器和客户端的Finished报文交换完毕之后，SSL连接就算建立完成。当然，通信会受到SSL的保护。从此处开始进行应用层协议的通信，即发送HTTP请求。
- 步骤11： 应用层协议通信，即发送HTTP响应。
- 步骤12： 最后由客户端断开连接。断开连接时，发送close_notify报文。上图做了一些省略，这步之后再发送TCP FIN报文来关闭与TCP的通信。

HTTPS也存在一些问题，那就是当使用SSL时，它的**处理速度会变慢**。

SSL的慢分两种。一种是指通信慢。另一种是指由于大量消耗CPU及内存等资源，导致处理速度变慢。
