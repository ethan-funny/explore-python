# HTTP 协议简介

**HTTP** (HyperText Transfer Protocol, 超文本传输协议)是互联网上应用最为广泛的一种网络协议，它是基于 [TCP][tcp] 的**应用层**协议，简单地说就是客户端和服务器进行通信的一种规则，它的模式非常简单，就是**客户端发起请求，服务器响应请求**，如下图所示：

![](https://ofaatpail.qnssl.com/2016-11-29-http.png-480)

HTTP 最早于 1991 年发布，是 0.9 版，不过目前该版本已不再用。HTTP 目前在使用的版本主要有：

- HTTP/1.0，于 1996 年 5 月发布，引入了多种功能，至今仍在使用当中。
- HTTP/1.1，于 1997 年 1 月发布，持久连接被默认采用，是目前最流行的版本。
- HTTP/2 ，于 2015 年 5 月发布，引入了服务器推送等多种功能，是目前最新的版本。

# HTTP 请求

HTTP 请求由三部分组成：

- **请求行**：包含请求方法、请求地址和 HTTP 协议版本
- **消息报头**：包含一系列的键值对
- **请求正文（可选）**：注意和消息报头之间有一个空行

如图所示：

![](https://ooo.0o0.ooo/2016/12/05/58456e61d5d47.png)

下面是一个 HTTP GET 请求的例子：

```
GET / HTTP/1.1
Host: httpbin.org
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.98 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Encoding: gzip, deflate, sdch, br
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4
Cookie: _ga=GA1.2.475070272.1480418329; _gat=1
```

上面的第一行就是一个**请求行**：

```
GET / HTTP/1.1
```

其中，`GET` 是请求方法，表示从服务器获取资源；`/` 是一个请求地址；`HTTP/1.1` 表明 HTTP 的版本是 1.1。

请求行后面的一系列键值对就是**消息报头**：

```
Host: httpbin.org
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.98 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Encoding: gzip, deflate, sdch, br
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4
Cookie: _ga=GA1.2.475034215.1480418329; _gat=1
```

其中：

- Host 是请求报头域，用于指定被请求资源的 Internet 主机和端口号，它通常从 HTTP URL 中提取出来；
- Connection 表示连接状态，keep-alive 表示该连接是持久连接（persistent connection），即 TCP 连接默认不关闭，可以被多个请求复用，如果客户端和服务器发现对方有一段时间没有活动，就可以主动关闭连接；
- Cache-Control 用于指定缓存指令，它的值有 no-cache, no-store, max-age 等，`max-age=秒`表示资源在本地缓存多少秒；
- User-Agent 用于标识请求者的一些信息，比如浏览器类型和版本，操作系统等；
- Accept 用于指定客户端希望接受哪些类型的信息，比如 text/html, image/gif 等；
- Accept-Encoding 用于指定可接受的内容编码；
- Accept-Language 用于指定可接受的自然语言；
- Cookie 用于维护状态，可做用户认证，服务器检验等，它是浏览器储存在用户电脑上的文本片段；

## HTTP 请求方法

HTTP 通过不同的请求方法以多种方式来操作指定的资源，常用的请求方法如下表：

| 方法 | 描述 |
| :-- | :-- |
| GET | 从服务器获取指定（请求地址）的资源的信息，它通常只用于读取数据，就像数据库查询一样，不会对资源进行修改。 |
| POST | 向指定资源提交数据（比如提交表单，上传文件），请求服务器进行处理。数据被包含在请求正文中，这个请求可能会创建新的资源或更新现有的资源。 |
| PUT | 通过指定资源的唯一标识（在服务器上的具体存放位置），请求服务器创建或更新资源。 |
| DELETE | 请求服务器删除指定资源。 |
| HEAD | 与 GET 方法类似，从服务器获取资源信息，和 GET 方法不同的是，HEAD 不含有呈现数据，仅仅是 HTTP 头信息。HEAD 的好处在于，使用这个方法可以在不必传输全部内容的情况下，就可以获得资源的元信息（或元数据）。 |
| OPTIONS | 该方法可使服务器传回资源所支持的所有 HTTP 请求方法。 |

# HTTP 响应

HTTP 响应与 HTTP 请求相似，由三部分组成：

- **状态行**：包含 HTTP 协议版本、状态码和状态描述，以空格分隔
- **响应头**：即消息报头，包含一系列的键值对
- **响应正文**：返回内容，注意和响应头之间有一个空行

如图所示：

![](https://ooo.0o0.ooo/2016/12/05/58456e62d49d6.png)

下面是一个 HTTP GET 请求的响应结果：

```
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 29 Nov 2016 13:08:38 GMT
Content-Type: application/json
Content-Length: 203
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {}, 
  "headers": {
    "Host": "httpbin.org", 
    "User-Agent": "Paw/2.3.1 (Macintosh; OS X/10.11.3) GCDHTTPRequest"
  }, 
  "origin": "13.75.42.240", 
  "url": "https://httpbin.org/get"
}
```

上面的第一行就是一个**状态行**：

```
HTTP/1.1 200 OK
```

其中，`200` 是状态码，表示客户端请求成功，`OK` 是相应的状态描述。

状态码是一个三位的数字，常见的状态码有以下几类：

- 1XX 消息 -- 请求已被服务接收，继续处理
- 2XX 成功 -- 请求已成功被服务器接收、理解、并接受
    - 200 OK
    - 201 Created 已创建
    - 202 Accepted 接收
    - 203 Non-Authoritative Information 非认证信息
    - 204 No Content 无内容
- 3XX 重定向 -- 需要后续操作才能完成这一请求
    - 301 Moved Permanently 请求永久重定向
    - 302 Moved Temporarily 请求临时重定向
    - 304 Not Modified 文件未修改，可以直接使用缓存的文件
    - 305 Use Proxy 使用代理
- 4XX 请求错误 -- 请求含有词法错误或者无法被执行
    - 400 Bad Request 由于客户端请求有语法错误，不能被服务器所理解
    - 401 Unauthorized 请求未经授权。这个状态代码必须和WWW-Authenticate报头域一起使用
    - 403 Forbidden 服务器收到请求，但是拒绝提供服务。服务器通常会在响应正文中给出不提供服务的原因
    - 404 Not Found 请求的资源不存在，例如，输入了错误的URL
- 5XX 服务器错误 -- 服务器在处理某个正确请求时发生错误
    - 500 Internal Server Error 服务器发生不可预期的错误，导致无法完成客户端的请求
    - 503 Service Unavailable 服务器当前不能够处理客户端的请求，在一段时间之后，服务器可能会恢复正常
    - 504 Gateway Time-out 网关超时

状态行后面的一系列键值对就是消息报头，即响应头：

```
Server: nginx
Date: Tue, 29 Nov 2016 13:08:38 GMT
Content-Type: application/json
Content-Length: 203
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

其中：

- Server 包含了服务器用来处理请求的软件信息，跟请求报头域 User-Agent 相对应；
- Content-Type 用于指定发送给接收者（比如浏览器）的响应正文的媒体类型，比如 text/html, text/css, image/png, image/jpeg, video/mp4, application/pdf, application/json 等；
- Content-Length 指明本次回应的数据长度；

# 再议 POST 和 PUT

注意到，POST 和 PUT 都可用于创建或更新资源，然而，它们之间还是有比较大的区别：

- POST 所对应的 URI 并非创建的资源本身，而是资源的接收者，资源本身的存放位置由服务器决定；而 PUT 所对应的 URI 是要创建或更新的资源本身，它指明了具体的存放位置

比如，往某个站点添加一篇文章，如果**使用 POST 来创建资源**，可类似这样：

```
POST /articles HTTP/1.1

{
    "author": "ethan",
    "title": "hello world",
    "content": "hello world"
}
```

在上面，POST 对应的 URI 是 `/articles`，它是资源的接收者，而非资源的标识，如果资源被成功创建，服务器可以返回 `201 Created` 状态以及新建资源的位置，比如：

```
HTTP/1.1 201 Created
Location: /articles/abcdef123
```

我们如果知道新建资源的标识符，可以**使用 PUT 来创建资源**，比如：

```
PUT /articles/abcdef234 HTTP/1.1

{
    "author": "peter",
    "title": "hello world",
    "content": "hello world"
}
```

在上面，PUT 对应的 URI 是 `/articles/abcdef234`，它指明了资源的存放位置，如果资源被成功创建，服务器可以返回 `201 Created` 状态以及新建资源的位置，比如：

```
HTTP/1.1 201 Created
Location: /articles/abcdef234
```

- 使用 PUT 更新某一资源，需要更新资源的全部属性；而使用 POST，可以更新全部或一部分值

比如使用 PUT 更新地址为 `/articles/abcdef234` 的文章的标题，我们需要发送所有值：

```
PUT /articles/abcdef234 HTTP/1.1

{
    "author": "peter",
    "title": "hello python",
    "content": "hello world"
}
```

而使用 POST，可以更新某个域的值：

```
POST /articles/abcdef234 HTTP/1.1

{
    "title": "hello python"
}
```

- POST 是不幂等的，PUT 是幂等的，这是一个很重要的区别

> HTTP 方法的幂等性是指一次和多次请求某一个资源应该具有同样的**副作用**，注意这里是副作用，而不是返回结果。

**GET** 方法用于获取资源，不会改变资源的状态，不论调用一次还是多次都没有副作用，因此它是幂等的；**DELETE** 方法用于删除资源，有副作用，但调用一次或多次都是删除同个资源，产生的副作用是相同的，因此也是幂等的；**POST** 是不幂等的，因为两次相同的 POST 请求会在服务器创建两份资源，它们具有不同的 URI；**PUT** 是幂等的，对同一 URI 进行多次 PUT 的副作用和一次 PUT 是相同的。

# HTTP 特点

- 客户端/服务器模式
- 简单快速：客户端向服务器请求服务时，通过传送请求方法、请求地址和数据体（可选）即可
- 灵活：允许传输任意类型的数据对象，通过 Content-Type 标识
- 无状态：对事物处理没记忆能力

# 小结

- HTTP 是在网络上传输 HTML 的协议，用于浏览器和服务器的通信，默认使用 80 端口。
- URL 地址用于定位资源，HTTP 中的 GET, POST, PUT, DELETE 用于操作资源，比如查询，增加，更新等。
- GET, PUT, DELETE 是幂等的，POST 是不幂等的。
- POST VS PUT
    - 使用 PUT 创建资源需要提供资源的唯一标识（具体存放位置），POST 不需要，POST 的数据存放位置由服务器自己决定
    - 使用 PUT 更新某一资源，需要更新资源的全部属性；而使用 POST，可以更新全部或一部分值
    - POST 是不幂等的，PUT 是幂等的，这是一个很重要的区别
- GET 可提交的数据量受到 URL 长度的限制，HTTP 协议规范没有对 URL 长度进行限制，这个限制是特定的浏览器及服务器对它的限制。
- 理论上讲，POST 是没有大小限制的，HTTP 协议规范也没有进行大小限制，出于安全考虑，服务器软件在实现时会做一定限制。

# 参考资料

- [超文本传输协议 - 维基百科，自由的百科全书](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE#HTTP.2F1.1)
- [HTTP 协议入门 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2016/08/http.html)
- [HTTP幂等性概念和应用 | 酷 壳 - CoolShell.cn](http://coolshell.cn/articles/4787.html)
- [Http协议详解 - 简书](http://www.jianshu.com/p/e83d323c6bcc)
- [When to use PUT or POST - The RESTful cookbook](http://restcookbook.com/HTTP%20Methods/put-vs-post/)
- [To PUT or POST?](https://stormpath.com/blog/put-or-post)
- [全面解读HTTP Cookie](http://www.webryan.net/2011/08/wiki-of-http-cookie/)
- [HTTP cookies 详解 | bubkoo](http://bubkoo.com/2014/04/21/http-cookies-explained/)
- [HTTP 接口设计指北](https://github.com/bolasblack/http-api-guide)



[tcp]: https://zh.wikipedia.org/wiki/%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE


