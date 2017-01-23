# Web 开发


你可以理解为手机和手机应用的关系：
只有手机没有手机应用行不行？
只有手机应用，没有手机行不行？

简单的说，Web 服务器实现了 HTTP 协议的服务器，监听端口，接受客户端建立连接的请求，捐接收数据，并把把接收到的HTTP请求通过类似CGI，WSGI，Servlet的接口交给应用程序处理，而应用程序处理后的输出交给Web服务器，由Web服务器通过连接发送给客户端。

如果没有Web服务器，应用程序开发者可能需要自己实现处理HTTP请求的部分，每个应用程序的开发者应该关注自己的业务逻辑，把处理HTTP的细节交给底层的基础设施去做，这是一种分层的思想，也是一种关注点分离。这是因为应用处理逻辑和底层的网络协议都很复杂，也都在发生巨大的变化，比如是不是要支持HTTPS啦，是否要支持HTTP身份认证啦，甚至HTTP/2也出来啦，还有很多HTTP的细节这里不展开讲。讲底层网络协议和不同的应用逻辑混杂在一起，大大增加了复杂度。而复杂的问题是很难解决好的。

当然，现在也有不需要外面的Web服务器，这是因为有人开发了内嵌的Web服务器，作为类库供应用开发者使用。

反过来讲，只有Web服务器是提供不了定制的业务逻辑的，只能提供静态网页浏览和文件下载。

更多详情: http://www.zhihu.com/question/54867889/answer/141464319



[Web开发 | 廖雪峰 JavaScript Python Git 教程](https://wizardforcel.gitbooks.io/liaoxuefeng/content/py2/86.html)

# 参考资料

- [一起写一个 Web 服务器（1）- Python](http://python.jobbole.com/81524/)
- [一起写一个 Web 服务器（2）- Python](http://python.jobbole.com/81523/)
- [一起写一个 Web 服务器（3）- Python](http://python.jobbole.com/81820/)

- [从零开始搭建论坛（1）：Web服务器与Web框架](http://python.jobbole.com/85874/)
- [从零开始搭建论坛（2）：Web服务器网关接口](http://python.jobbole.com/85963/)
- [从零开始搭建论坛（3）：Flask框架简介](http://python.jobbole.com/86707/)

