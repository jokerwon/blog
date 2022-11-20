---
title: Web 性能优化
tag: [Frontend, JavaScript, HTTP, Devops, Webpack, 性能优化]
---


## 一、网络优化

HTTP 优化有两个大的方向：

- 减少请求次数
- 减少单次请求所花费的时间

<!-- more -->

### 1.1 Webpack 性能调优

webpack 的优化可以体现在两方面：

- webpack 的构建速度
- webpack 的构建体积

#### 1.1.1 提升构建速度

##### 1. 不要让 loader 做太多事情

以 babel-loader 为例。

~~~javascript
module: {
  rules: [
    {
      test: /\.js$/,
      exclude: /(node_modules|bower_components)/,
      use: {
        loader: 'babel-loader?cacheDirectory=true',
        options: {
          presets: ['@babel/preset-env']
        }
      }
    }
  ]
}
~~~

`exclude` 属性告知 `babel-loader` 不要对庞大的 node_modules 或 bower_components 文件夹进行处理。loader 的属性值 `babel-loader?cacheDirectory=true`可以开启缓存将转译结果缓存至文件系统。

##### 2. 不要放过第三方库

处理第三方的插件有 Externals、CommonsChunkPlugin、DllPlugin 等。推荐使用 DllPlugin。DllPlugin 是基于 Windows 动态链接库（dll）的思想被创作出来的。这个插件会把第三方库单独打包到一个文件中，这个文件就是一个单纯的依赖库。**这个依赖库不会跟着你的业务代码一起被重新打包，只有当依赖自身发生版本变化时才会重新打包**。

##### 3. 将 loader 由单线程转为多线程

推荐使用插件 **Happypack**

#### 1.1.2 压缩构建体积

##### 1. 使用可视化工具分析导致导致体积过大的原因

推荐使用插件 [webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer) 。

##### 2. 拆分资源（参见上一节DllPlugin）

##### 3. 删除冗余代码

一个比较典型的应用，就是 `Tree-Shaking`。

推荐使用 UglifyJsPlugin 。

##### 4. 按需加载

Vue 中可以在路由中配合 Webpack 的特殊注释进行路由懒加载。

第三方库例如 ElementUI 配合官方文档的配置进行按需加载组件。

##### 5. 通过 CDN 来使用第三方库

### 1.2 Gzip 压缩技术

### 1.3 图片优化

#### 1. JPEG / JPG

**有损压缩、体积小、加载快、不支持透明**

#### 2. PNG-8 和 PNG-24

**无损压缩、质量高、体积大、支持透明**

#### 3. SVG

**文本文件、体积小、不失真、兼容性好**

#### 4. Base64 和 雪碧图

**文本文件、依赖编码、小图标解决方案**

webpack 的 [url-loader](https://github.com/webpack-contrib/url-loader)

#### 5. WebP

WebP 像 JPEG 一样对细节丰富的图片信手拈来，像 PNG 一样支持透明，像 GIF 一样可以显示动态图片——它集多种图片文件格式的优点于一身。但是兼容性差。

## 二、存储优化

### 2.1 浏览器缓存机制介绍与缓存策略剖析

通过网络获取内容既速度缓慢又开销巨大。较大的响应需要在客户端与服务器之间进行多次往返通信，这会延迟浏览器获得和处理内容的时间，还会增加访问者的流量费用。因此，缓存并重复利用之前获取的资源的能力成为性能优化的一个关键方面。

浏览器缓存机制有四个方面，它们按照获取资源时请求的优先级依次排列如下：

1. Memory Cache
2. Service Worker Cache
3. HTTP Cache
4. Push Cache

#### 2.1.1 HTTP 缓存机制

HTTP 缓存是我们日常开发中最为熟悉的一种缓存机制。它又分为**强缓存**和**协商缓存**。优先级较高的是强缓存，在命中强缓存失败的情况下，才会走协商缓存。

##### 1. 强缓存

强缓存是利用 http 头中的 Expires 和 Cache-Control 两个字段来控制的。强缓存中，当请求再次发出时，浏览器会根据其中的 expires 和 cache-control 判断目标资源是否“命中”强缓存，若命中则直接从缓存中获取资源，**不会再与服务端发生通信。**

命中强缓存的情况下，返回的 HTTP 状态码为 200 （如下图）。

![img](https://user-gold-cdn.xitu.io/2018/9/20/165f6a683fc021e1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

在 HTTP1.1 之前，通过 `expires` 字段来实现强缓存。当服务器返回响应时，在 Response Headers 中将过期时间写入 expires 字段。例如这样：

~~~
expires: Wed, 11 May 2020 22:11:00 GMT
~~~

`expires` 是一个时间戳，当本地时间早于这个时间时，客户端才会去从缓存中获取资源，否则就发送请求到服务器，获取最新资源。`expires` 存在一个问题，如果服务端和客户端的时间设置可能不同，或者我直接手动去把客户端的时间改掉，那么 expires 将无法达到我们的预期。

考虑到 expires 的局限性，HTTP1.1 新增了 `Cache-Control` 字段来完成 expires 的任务。
expires 能做的事情，Cache-Control 都能做；expires 完成不了的事情，Cache-Control 也能做。因此，Cache-Control 可以视作是 expires 的**完全替代方案**。在当下的前端实践里，我们继续使用 expires 的唯一目的就是**向下兼容**。

~~~
cache-control: max-age=1800000
~~~

`cache-control` 通过指定一个时间长度，来限制缓存的新鲜期。在这个新鲜期内，缓存都是有效的。

**Cache-Control 相对于 expires 更加准确，它的优先级也更高。当 Cache-Control 与 expires 同时出现时，我们以 Cache-Control 为准。**

`cache-control` 还可以指定其他属性。

~~~
cache-control: max-age=3600, s-maxage=31536000
~~~

在依赖各种**代理**的大型架构中，我们不得不考虑**代理服务器**的缓存问题。s-maxage 就是用于表示 cache 服务器上（比如 cache CDN）的缓存的有效时间的，并只对 public 缓存有效。

**public 与 private**

public 与 private 是针对资源是否能够被代理服务缓存而存在的一组对立概念。

如果我们为资源设置了 public，那么它既可以被浏览器缓存，也可以被代理服务器缓存；如果我们设置了 private，则该资源只能被浏览器缓存。private 为**默认值**。但多数情况下，public 并不需要我们手动设置，比如有很多线上网站的 `cache-control`是这样的：

![img](https://user-gold-cdn.xitu.io/2018/9/20/165f6029fc74bbc6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

设置了 `s-maxage`，没设置 public，那么 CDN 还可以缓存这个资源吗？答案是肯定的。因为明确的缓存信息（例如“max-age”）已表示响应是可以缓存的。

**no-store与no-cache**

no-cache 绕开了浏览器：我们为资源设置了 no-cache 后，每一次发起请求都不会再去询问浏览器的缓存情况，而是直接向服务端去确认该资源是否过期（即走我们下文即将讲解的协商缓存的路线）。

no-store 比较绝情，顾名思义就是不使用任何缓存策略。在 no-cache 的基础上，它连服务端的缓存确认也绕开了，只允许你直接向服务端发送请求、并下载完整的响应。

##### 2. 协商缓存

协商缓存指浏览器与服务器合作之下的缓存策略，依赖于服务端与浏览器之间的通信。

协商缓存机制下，浏览器需要向服务器去询问缓存的相关信息，进而判断是重新发起请求、下载完整的响应，还是从本地获取缓存的资源。

如果服务端提示缓存资源未改动（Not Modified），资源会被**重定向**到浏览器缓存，**这种情况下网络请求对应的状态码是 304 Not Modified**。

**Last-Modified**

`Last-Modified` 是一个时间戳，当启用协商缓存时，响应的首部会携带此字段。

```
Last-Modified: Fri, 27 Oct 2020 22:11:00 GMT
```

后面每次请求该资源时，请求首部就会将该字段的值携带在 `If-Modified-Since` 字段中。

```
If-Modified-Since: Fri, 27 Oct 2020 22:11:00 GMT
```

服务器接收到这个时间戳后，会比对该时间戳和资源在服务器上的最后修改时间是否一致，从而判断资源是否发生了变化。如果发生了变化，就会返回一个完整的响应内容，并在响应首部中更新 `Last-Modified` ；否则，返回 `304 Not Modified` 响应，响应报文也不会携带响应实体而且首部也不会再携带 `Last-Modified` 字段。

使用 `Last-Modified` 存在一些弊端，这其中最常见的就是这样两个场景：

- 我们编辑了文件，但文件的内容没有改变。服务端并不清楚我们是否真正改变了文件，它仍然通过最后编辑时间进行判断。因此这个资源在再次被请求时，会被当做新资源，进而引发一次完整的响应——不该重新请求的时候，也会重新请求。
- 当我们修改文件的速度过快时（比如花了 100ms 完成了改动），由于 `If-Modified-Since` 只能检查到以秒为最小计量单位的时间差，所以它是感知不到这个改动的——该重新请求的时候，反而没有重新请求了。

此二种场景都阐述了同一个问题——服务器没有正确感知文件的变化。`Etag` 应运而生。

**Etag**

Etag 是由服务器为每个资源生成的唯一的**标识字符串**，这个标识字符串是基于文件内容编码的，只要文件内容不同，它们对应的 Etag 就是不同的，反之亦然。因此 Etag 能够精准地感知文件的变化。

~~~
ETag: W/"2a3b-1602480f459"
~~~

在下一次请求该资源时，请求头中会使用 `If-None-Match`字段将响应头中 `Etag`的值携带。

 **Etag 在感知文件变化上比 Last-Modified 更加准确，优先级也更高。当 Etag 和 Last-Modified 同时存在时，以 Etag 为准。**

#### 2.1.2 HTTP 缓存策略建议

![img](https://user-gold-cdn.xitu.io/2018/9/20/165f701820fafcf8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

当我们的资源内容不可复用时，直接为 Cache-Control 设置 no-store，拒绝一切形式的缓存；否则考虑是否每次都需要向服务器进行缓存有效确认，如果需要，那么设 Cache-Control 的值为 no-cache；否则考虑该资源是否可以被代理服务器缓存，根据其结果决定是设置为 private 还是 public；然后考虑该资源的过期时间，设置对应的 max-age 和 s-maxage 值；最后，配置协商缓存需要用到的 Etag、Last-Modified 等参数。

#### 2.1.3 Memory Cache

MemoryCache，是指存在内存中的缓存。从优先级上来说，它是浏览器最先尝试去命中的一种缓存。从效率上来说，它是响应速度最快的一种缓存。

内存缓存是快的，也是“短命”的。它和渲染进程“生死相依”，当进程结束后，也就是 tab 关闭以后，内存里的数据也将不复存在。

#### 2.1.4 Service Worker Cache

Service Worker 是一种独立于主线程之外的 Javascript 线程。它脱离于浏览器窗体，因此无法直接访问 DOM。这样独立的个性使得 Service Worker 的“个人行为”无法干扰页面的性能，这个“幕后工作者”可以帮我们实现离线缓存、消息推送和网络代理等功能。我们借助 Service worker 实现的离线缓存就称为 Service Worker Cache。

Service Worker 的生命周期包括 install、active、working 三个阶段。一旦 Service Worker 被 install，它将始终存在，只会在 active 与 working 之间切换，除非我们主动终止它。这是它可以用来实现离线存储的重要先决条件。

#### 2.1.5 Push Cache

- Push Cache 是缓存的最后一道防线。浏览器只有在 Memory Cache、Service Worker Cache 和 HTTP Cache  均未命中的情况下才会去询问 Push Cache。
- Push Cache 是一种存在于会话阶段的缓存，当 session 终止时，缓存也随之释放。
- 不同的页面只要共享了同一个 HTTP2 连接，那么它们就可以共享同一个 Push Cache。

### 2.2 本地存储

#### 2.2.1 Cookie

Cookie 说白了就是一个存储在浏览器里的一个小小的文本文件，它附着在 HTTP 请求上，在浏览器和服务器之间“飞来飞去”。它可以携带用户信息，当服务器检查 Cookie 的时候，便可以获取到客户端的状态。

**Cookie 以键值对的形式存在**。

**Cookie 的性能劣势**

- 体积小。最大只能有 4KB。

- 过量的 Cookie 会带来巨大的性能浪费

  <u>Cookie 是紧跟域名的</u>。我们通过响应头里的 Set-Cookie 指定要存储的 Cookie 值。默认情况下，domain 被设置为设置 Cookie 页面的主机名，我们也可以手动设置 domain 的值：

  ~~~
  Set-Cookie: name=jokerwon; domain=jokerwon.io
  ~~~

- 同一个域名下的所有请求，都会携带 Cookie。

#### 2.2.2 Web Storage: Local Storage 与 Session Storage

Web Storage 是 HTML5 专门为浏览器存储而提供的数据存储机制。它又分为 Local Storage 与 Session Storage。它有以下特点：

- 存储容量大： Web Storage 根据浏览器的不同，存储容量可以达到 5-10M 之间。
- 仅位于浏览器端，不与服务端发生通信。

Local Storage 与 Session Storage 的区别：

- 生命周期：Local Storage 是持久化的本地存储，存储在其中的数据是永远不会过期的，使其消失的唯一办法是手动删除；而 Session Storage 是临时性的本地存储，它是会话级别的存储，当会话结束（页面被关闭）时，存储内容也随之被释放。
- 作用域：Local Storage、Session Storage 和 Cookie 都遵循同源策略。但 Session Storage 特别的一点在于，即便是相同域名下的两个页面，只要它们**不在同一个浏览器窗口中**打开，那么它们的 Session Storage 内容便无法共享。

#### 2.2.3 IndexedDB

IndexedDB 是一个**运行在浏览器上的非关系型数据库**。理论上来说，IndexedDB 是没有存储上限的（一般来说不会小于 250M）。它不仅可以存储字符串，还可以存储二进制数据。

### 2.3 CDN 的缓存和回源策略

#### 2.3.1 CDN的核心功能特写

CDN 的核心点有两个，一个是**缓存**，一个是**回源**。

这两个概念都非常好理解。对标到上面描述的过程，“缓存”就是说我们把资源 copy 一份到 CDN 服务器上这个过程，“回源”就是说 CDN 发现自己没有这个资源（一般是缓存的数据过期了），转头向根服务器（或者它的上层服务器）去要这个资源的过程。

#### 2.3.2 CDN 与前端性能优化

**CDN 往往被用来存放静态资源**。上文中我们举例所提到的“根服务器”本质上是业务服务器，它的核心任务在于**生成动态页面或返回非纯静态页面**，这两种过程都是需要计算的。业务服务器仿佛一个车间，车间里运转的机器轰鸣着为我们产出所需的资源；相比之下，CDN 服务器则像一个仓库，它只充当资源的“栖息地”和“搬运工”。
