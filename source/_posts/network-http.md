---
title: Http VS Https
date: 2018-06-22 01:14:25
categories: Network
tag: [Http,Https]
---
## 基础名称
### 请求报文
客户端发送一个HTTP请求到服务器的请求消息包括以下格式：  
请求行（request line）、请求头（header）、请求内容组成，如下请求报文的一般格式。
![请求报文](https://res.cloudinary.com/incoder/image/upload/v1562212437/blog/newwork-request.webp)

#### 请求行
1. 方法：
    * GET： 获取资源
    * POST： 向服务器端发送数据，传输实体主体
    * PUT： 传输文件
    * HEAD： 获取报文首部
    * DELETE： 删除文件
    * OPTIONS： 询问支持的方法
    * TRACE： 追踪路径
2. URL：
    `scheme://host:port/path?query`
    * scheme: 表示协议，如Http, Https, Ftp等
    * host: 表示所访问资源所在的主机名：如：www.baidu.com
    * port: 表示端口号，Http默认为80,Https默认为443
    * path: 表示所访问的资源在目标主机上的储存路径
    * query: 表示查询条件

3. 协议/版本号：

#### 请求头
1. 通用首部(General Header)
2. 请求首部(Request Header)
3. 实体首部(Entity Header Fields)

#### 请求内容
如: 客户端POST的数据就放在这里(对比:GET的数据放在请求行的URL里)

例如：
![请求示例](https://res.cloudinary.com/incoder/image/upload/v1529805798/blog/http-request.png)

### 响应报文
服务端响应一个HTTP请求消息包括以下格式：
响应行（response line）、响应头（header）、响应内容组成

#### 响应行
1. 状态码：
    * 1XX：Informational(信息性状态码)
    * 2XX：Success(成功状态码)
    * 3XX：Redirection(重定向)
    * 4XX：Client Error(客户端错误状态码)
    * 5XX：Server Error(服务器错误状态吗)
2. 状态码描述：
3. 协议/版本号：

#### 响应头
1. 通用首部(General Header)
2. 响应首部(Response Header)
3. 实体首部(Entity Header Fields)

#### 响应内容
如：服务器返回的HTML、JSON等数据

![响应示例](https://res.cloudinary.com/incoder/image/upload/v1529805798/blog/http-response.png)

## Http
### 概念
* [HTTP](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)：超文本传输协议（HyperText Transfer Protocol）是一种用于分布式、协作式和超媒体信息系统的应用层协议.
* HTTP是万维网的数据通信的基础.

### 通信
1. 建立TCP连接
在HTTP工作开始之前，Client首先要通过网络与Service建立连接，该连接是通过TCP来完成的，HTTP是比TCP更高层次的应用层协议，根据规则，只有低层协议建立之后才能进行更高层协议的连接，因此，首先要建立TCP连接
2. Client发起HTTP请求(Request)
Requset通常包含请求行，请求头，请求内容这三部风组成的请求报文
3. Service发送HTTP响应(Response)
Response通常包含响应行，响应头，响应内容这三部风组成的响应报文
4. Client关闭TCP连接

### 特点
1. 无状态
    * 每个请求结束后都会被关闭，每次的请求都是独立的，它的执行情况和结果与前面的请求和之后的请求是无直接关系的，它不会受前面的请求应答情况直接影响，也不会直接影响后面的请求应答情况
    * 服务器中没有保存客户端的状态，客户端必须每次带上自己的状态去请求服务器
2. 明文传输，可能被窃听
3. 不验证通信方的身份，可能遭遇伪装
    * HTTP 协议中的请求和响应不会对通信方进行确认。也就是说存在“服务器是否就是发送请求中 URI 真正指定的主机，返回的响应是否真的返回到实际提出请求的客户端”等类似问题
    *  HTTP 协议通信时，由于不存在确认通信方的处理步骤，任何人都可以发起请求
4. 无法证明报文的完整性，可能遭遇篡改
    * 在请求或响应送出之后直到对方接收之前的这段时间内，即使请求或响应的内容遭到篡改，也没有办法获悉

## Https
### 概念
* [HTTPS](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%AE%89%E5%85%A8%E5%8D%8F%E8%AE%AE)：超文本传输安全协议（Hypertext Transfer Protocol Secure，常称为HTTP over TLS，HTTP over SSL或HTTP Secure）是一种通过计算机网络进行安全通信的传输协议.
* HTTPS经由HTTP进行通信，但利用`SSL/TLS`来加密数据包.

> HTTP+加密+认证+完整性保护 = HTTPS

![HTTP VS HTTPS](https://res.cloudinary.com/incoder/image/upload/v1529822573/blog/HTTPS.png)

### 通信
#### SSL/TLS
SSL/TLS：安全传输层协议(Transport Layer Security), 是介于TCP和HTTP之间的一层安全协议，TLS的前身是SSL(Secure Sockets Layer)

>TLS/SSL关系
* SSL2.0
* SSL3.0
* TLS1.0(SSL3.1)
* TLS1.1(SSL3.2)
* TLS1.2(SSL3.3)

#### SSL/TLS工作原理
HTTPS协议的主要功能都依赖于SSL/TLS协议，SSL/TLS的功能实现主要依赖于三类算法：`对称加密`,`非对称加密`,`散列函数Hash`
* 非对称加密实现身份认证和密钥协商，
* 对称加密算法采用协商的密钥对数据加密，
* 基于散列函数验证信息的完整性

#### SSL/TLS协议实现
TLS以记录协议(record protocol)实现。记录协议负责在传输连接上交换所有的底层消息，并可以配置加密。每一条TLS记录以一个短标头起始。标头包含记录内容的类型(或子协议)、协议版本和长度

TLS的主规格说明书定义了四个核心子协议：

* 握手协议(handshake protocol)；
* 密钥规格变更协议(change cipher spec protocol)；
* 应用数据协议(application data protocol)；
* 警报协议(alert protocol)；

#### 握手协议
握手是TLS协议中最精密复杂的部分。在这个过程中，通信双方协商连接参数，并且完成身份验证。根据使用的功能的不同，整个过程通常需要交换6~10条消息。根据配置和支持的协议扩展的不同，交换过程可能有许多变种，在使用中经常可以观察到以下三种流程：
* 单向验证(完整的握手，对服务器进行身份验证)
* 双向验证(对客户端和服务器都进行身份验证的握手)
* 简短握手(恢复之前的会话)

##### 单向验证
![单向验证](https://blog-10039692.file.myqcloud.com/1494841223417_6503_1494841223715.png)
1. Handshake:ClentHello
客户端通过发送 Client Hello 报文开始 SSL通信。报文中包含客户端支持的 SSL的指定版本、加密组件（Cipher Suite）列表（所使用的加密算法及密钥长度等）。
2. Handshake:ServerHello
服务器可进行 SSL通信时，会以 ServerHello 报文作为应答。和客户端一样，在报文中包含 SSL版本以及加密组件。服务器的加密组件内容是从接收到的客户端加密组件内筛选出来的。
3. Handshake:Certificate
之后服务器发送 Certificate 报文。报文中包含公开密钥证书。
4. Handshake:ServerHelloDone
最后服务器发送 ServerHelloDone 报文通知客户端，最初阶段的 SSL握手协商部分结束。
5. Handshake:ClientKeyExchange
SSL第一次握手结束之后，客户端以 ClientKeyExchange 报文作为回应。报文中包含通信加密中使用的一种被称为 Pre-mastersecret 的随机密码串。该报文已用3 中的公开密钥进行加密。
6. ChangeCipherSpec
接着客户端继续发送 ChangeCipherSpec 报文。该报文会提示服务器，在此报文之后的通信会采用 Pre-master secret 密钥加密。
7. Handshake:Finished
客户端发送 Finished 报文。该报文包含连接至今全部报文的整体校验值。这次握手协商是否能够成功，要以服务器是否能够正确解密该报文作为判定标准。
8. ChangeCipherSpec
服务器同样发送 ChangeCipherSpec 报文。
9. Handshake:Finished
服务器同样发送 Finished 报文。
10. Application Data(HTTP)
服务器和客户端的 Finished 报文交换完毕之后，SSL连接就算建立完成。当然，通信会受到 SSL的保护。从此处开始进行应用层协议的通信，即发送 HTTP 请求。
11. Application Data(HTTP)
应用层协议通信，即发送 HTTP 响应。
12. Alert:warning,close notify
最后由客户端断开连接。断开连接时，发送 close_notify 报文(上图做了一些省略，实际到这一步还需要发送TCP FIN报文关闭TCP链接) 

##### 双向验证
![双向验证](https://blog-10039692.file.myqcloud.com/1494841503771_6933_1494841504095.png)
同单向验证流程相比，双向验证多了如下两条消息：`CertificateRequest`与`CertificateVerify`，其余流程大致相同
* CertificateRequest
CertificateRequest是TLS规定的一个可选功能，用于服务器认证客户端的身份。通过服务器要求客户端发送一个证书实现，服务器应该在ServerKeyExchange之后立即发送CertificateRequest消息
* CertificateVerify
当需要做客户端认证时，客户端发送CertificateVerify消息，来证明自己确实拥有客户端证书的私钥。这条消息仅仅在客户端证书有签名能力的情况下发送

#### 应用数据协议(application data protocol)
应用数据协议携带着应用消息，只以TLS的角度考虑的话，这些就是数据缓冲区。记录层使用当前连接安全参数对这些消息进行打包、碎片整理和加密

#### 警报协议(alert protocol)
警报的目的是以简单的通知机制告知对端通信出现异常状况。它通常会携带close_notify异常，在连接关闭时使用，报告错误

## 附录
* 《图解HTTP》
* [HTTP | MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Overview)
* [数字证书及CA的扫盲介绍](https://kb.cnblogs.com/page/194742)
* [HTTPS 原理浅析及其在 Android 中的使用](https://cloud.tencent.com/developer/article/1005073)