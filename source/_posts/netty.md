---
title: Netty初体验（一）
date: 2019-11-20 10:32:10
categories: Netty
tag: [Netty]
---

[Netty](https://netty.io) 是国内外各大互联网公司的必备网络应用框架，Netty 主要处理与网络相关的一些应用。由于 Netty 设计的巧妙的实现方式，以及对协议很好的实现，使的 Netty 可以在各种应用场景下广泛的应用，无论是传统基于HTTP协议的访问方式，还是更底层基于socket的访问方式，以及支持HTML5规范中的websocket的长连接特性，都提供了比较好的支持

Netty is an asynchronous event-driven network application framework for rapid development of maintainable high performance protocol servers & clients（Netty 是一个异步的，事件驱动的网络应用框架，是可维护的，高性能的，协议化的服务端和客户端快速开发方式）

<!-- more -->

Netty 是一个非阻塞(NIO)客户端服务端框架，它可以快速的进行网络应用开发，例如：基于协议的客户端和服务端。它极大的简化并且支持流式的网络程序，例如：基于TCP和UDP的socket服务
'快捷方便'并不意味着最终的应用程序会受到可维护性或性能问题的影响。Netty经过精心设计，具有实施许多协议所获得的经验，如FTP，SMTP，HTTP以及各种基于二进制和文本的遗留协议。因此，Netty 成功地找到了一种在不妥协的情况下实现易于开发，性能，稳定性和灵活性的方法

## 特点

* 适用于各种传输类型统一API - 阻塞和非阻塞 socket
* 基于灵活且可扩展的事件模型，可以清晰地关注分离(separation of concerns)
* 高度可定制的线程模型 - 单线程，一个或多个线程池，如:Staged Event Driven Architecture(SEDA,阶段型事件驱动架构，将一个请求分成若干个阶段，每个阶段可以根据自身情况不用数量的线程来分别进行处理，阶段与阶段之间是通过事件驱动的这种异步通讯模式来进行沟通及通信)
* 真正的无连接数据报套socket支持（since 3.1）

## 性能

* 更高的吞吐量，更低的延迟
* 减少资源消耗
* 不必要的内存复制降到最低

## 安全

* 完整的SSL / TLS 和 StartTLS 支持

## Netty 使用场景

1. 作为HTTP的服务器，类似与Jetty，Tomcat这种Servlet容器，只是Netty在充当HTTP的服务器时，它采用的编程模型并不是基于Servlet的规范，原因是Netty并没有实现Servlet的接口，Servlet的实现，Netty有自己的实现方式
2. 作为RPC通讯的框架，通讯的协议（可自定义），通讯的库，实现远程过程的调用，基于Socket方式（广泛使用）
3. 作为长连接的服务器，基于WebSocket，实现客户端和服务端之间的长连接通信

环境说明
* System：macOS
* Java：JDK1.8+
* Netty：4.1.25.Final

### HTTP

Netty 基于Servlet的规范，是一种特定的方式（更底层），因此 Netty 更专注于底层的性能等方面，所以在应用层开发时，是由开发人员自行去组装对请求，对 **请求路由** 处理等

> 示例：[HTTP](https://github.com/RootCluster/rc-cluster-netty/tree/master/src/main/java/org/incoder/netty/http)

### Socket

Socket 是计算机网络中用于在节点内发送或接收数据的内部端点。具体来说，它是网络软件 (协议栈) 中这个端点的一种表示，包含通信协议、目标地址、状态等，是系统资源的一种形式

> 示例：[Socket](https://github.com/RootCluster/rc-cluster-netty/tree/master/src/main/java/org/incoder/netty/socket)

### Websocket

WebSocket 是HTML5 规范的一部分，也是基于 HTTP 协议之上的一种协议，WebSocket主要是解决 HTTP 上存在的一些问题；
1. HTTP 一种无状态（同一客户端发出的第一次请求接收到响应后，客户端发送第二次请求，这两次请求之间没有任何关联）的协议，HTTP 无法追踪某一请求来自哪一个客户端，客户端之前在服务器上存在一些信息（常见的解决方式：cookie，session）
2. HTTP 是基于请求响应模式的协议，请求的发起方一定是客户端，服务器将响应返回给客户端后连接就断掉了（HTTP 1.0），在 1.0 的基础上连接可以短时间的保持，一种 keep-alive 机制（HTTP 1.1）

通常我们所使用的长连接技术
* 早期采用轮询的方式保持与服务器的连接
* 目前通常采用 Websocket 的连接方式保持与服务器的连接
  * 客户端（浏览器）与服务器建立连接后，没有其他因素干扰，连接是不会断，一直存在，客户端与服务器双方是对等的，不再区分谁是客户端，谁是服务端，客户端可以发送数据给服务端，服务端也可以发送数据给客户端，在真正意义上实现了服务端的推技术
  * 长连接在建立初期会发送带有 header 头信息的网络请求，在连接建立后，在长连接之上只需要发送需要传递的数据（真正的数据）即可
  * Websocket 是基于 HTTP 协议
  * Websocket 也可以用于非浏览器的场景，只要你的库支持 Websocket 即可

> 示例：
> * [Websocket Server](https://github.com/RootCluster/rc-cluster-netty/tree/master/src/main/java/org/incoder/netty/websocket)
> * [Websocket Client](https://github.com/RootCluster/rc-cluster-netty/blob/master/src/webapp)

### Heartbeat

对于服务器上的集群服务（zookeeper 或者其他的应用服务），或者是客户端与服务端之间的长连接，需要一种机制来检测客户端还是 alive，这种机制就是 heartbeat
* 对于服务器上的这些服务与服务之间，节点与节点之间的通信（无一例外都使用 TCP 连接通信），节点之间的通信如何保证（A 节点感知到 B 或者其他节点是未宕机），此时就需要心跳来检测对应的服务或节点还是正常的
* 对于客户端与服务器之间由于网络问题，或者客户端开启飞行模式，或者关机等状态，服务端是无法感知，因此也需要借助心跳来检测客户端是否关机或开启了飞行模式

> 示例：[Heartbeat](https://github.com/RootCluster/rc-cluster-netty/tree/master/src/main/java/org/incoder/netty/heartbeat)

## 总结

### Netty 程序编写步骤

1. 定义好父子的（bossGroup：获取链接,workerGroup：真正来处理链接）线程组（EventLoopGroup）,服务器启动时关联一个处理器处理器类似Initializer这样的处理器
2. Initializer定义好自定义的或Netty本身提供的ChannelHandler通道处理器，在initChannel中自定义添加若干个处理器
3. 实现自定义处理器ChannelHandler中特定的回调方法

### Netty 程序测试
1. 启动 Server 服务
2. 访问启动的服务，使用 curl命令 或者使用浏览器访问进行访问

![netty-test](https://res.cloudinary.com/incoder/image/upload/v1575207499/blog/netty-http.png)