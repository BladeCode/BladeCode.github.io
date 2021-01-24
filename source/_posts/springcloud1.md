---
title: SpringCloud（一）Security OAuth2
date: 2020-07-11 07:10:00
categories: SpringCloud
tag: [SpringCloud, OAuth2]
---

## 什么是 OAuth2

用于 REST/APIs 的代理授权框架（**delegated authorization** framework），基于令牌 Token 的授权，在无需暴露用户密码的情况下，使应用能获取对用户数据的有限访问权限，做到解耦认证和授权

OAuth 是一个开放标准，该标准允许用户让第三方应用访问该用户在某一网站上存储的私密资源（如：头像，照片，视频等），而在这个过程中无线将用户名和密码提供给第三方应用。实现这一功能是通过提供一个令牌（token），而不是用户名和密码来访问他们存放在特定服务提供者的数据

<!-- more -->

## 什么是 Spring Security

Spring Security 是为基于 Spring 的应用程序提供声明书安全保护的安全性框架。Spring Security 提供了完整的安全性解决方案，它能够在 Web 请求级别和方法调用级别处理身份认证和授权。因为基于 Spring 框架，所以 Spring Security 充分利用了依赖注入（Dependency Injection，DI）和面向切面的技术

最初，Spring Security 被称为 Acegi Security。Acegi 是一个强大的安全框架，但是它存在一个严重的问题，那就是需要大量的 XML 配置。到了 2.0 版本，Acegi Security 更名为 Spring Security，2.0版本所带来的不仅仅是名字的变化。为了在 Spring 中配置安全性，Spring Security 引入一个全新的、与安全性相关的 XML 命名空间。这个新的命名空间联通注解和一些合理的默认设置，将典型的安全性配置从几百行 XML 减少到十几行。Spring Security 3.0 融入了 SpEL，这将进一步简化  路安全性配置

Spring Security 从两个角度来解决安全问题。
* 它使用 Servlet 规范中的 Filter 保护 Web 请求并限制 URL 级别的访问
* 它还能够使用 Spring AOP 保护方法调用——借助于对象代理和使用通知，能够确保只有具备适当权限的用户才能访问安全保护的方法

## Spring Security OAuth 现状

[Spring Security OAuth](https://spring.io/projects/spring-security-oauth) 的模块已被废弃，后续功能已经迁移到 Spring Security 5.2.x 中，但不会再提供 Authorization Server 的功能。

为此，随着 Spring Security5.2 发布，Spring 官方强烈鼓励用户开始将其旧版 OAuth2 客户端和资源服务器应用迁移到 Spring Security5.2 中的新支持

[具体的功能列表](https://github.com/spring-projects/spring-security/wiki/OAuth-2.0-Features-Matrix)请移步查看

## OAuth2 主要角色

1. 客户应用（Client Application）：通常是一个 Web 或无线应用，它需要访问用户的受保护资源
2. 资源服务器（Resource Server）：是一个 Web 站点或者 Web service API，用户的受保护数据保存于此
3. 授权服务器（Authorized Server）：在客户应用成功认证并获得授权之后，向客户应用颁发访问令牌 Access Token
4. 资源拥有者（Resource Owner）：资源的拥有人，想要分享某些资源给第三方应用
5. 客户凭证（Client Credentials）：客户的 clientId 和密码用于认证客户
6. 令牌（Tokens）：授权服务器在接收到客户请求后，颁发的访问令牌
    * 授权码（Authorization Code Token）：仅用于授权码授权类型，用于交换获取访问令牌和刷新令牌
    * 刷新令牌（Refresh Token）：用于去授权服务器获取一个新的访问令牌
    * **访问令牌（Access Token）**：用于代表一个用户或服务直接去访问受保护的资源
    * Bearer Token：不管谁拿到 Token 都可以访问资源，像现钞
    * Proof of Possession(PoP) Token：可以校验 client 是否对 Token 有明确的拥有权
7. 作用域（Scopes）：客户请求访问令牌时，由资源拥有者额外指定的细分权限

## OAuth2 误解

* OAuth 并没有支持 **HTTP 以外的协议**
* OAuth 并不是一个 **认证协议**
* OAuth 并没有定义 **授权处理机制**
* OAuth 并没有定义 **Token 格式**
* OAuth 2.0 并没有定义 **加密方法**
* OAuth 2.0 并不是 **单个** 协议
* OAuth 2.0 仅是 **授权框架**，仅用于授权代理

### OAuth2 运行流程

![来自 RFC 6749](https://res.cloudinary.com/incoder/image/upload/v1594815003/blog/OAuth2.png)

从上面的流转过程，经过下面六个步骤，客户端就能获取到访问资源的令牌，其中第二步是关键，用户要怎样才能给予客户端授权（客户端获取授权的四种模式）

1. （A）用户打开客户端后，客户端要求用户给予权限
2. （B）用户同意给予客户端权限
3. （C）客户端使用上一步获取的授权，向认证服务器申请令牌
4. （D）认证服务器对客户端进行认证后，确认无误，同意发放令牌
5. （E）客户端使用令牌，向资源服务器申请获取资源
6. （F）资源服务器确认令牌无误，同意向客户端开放资源

### Access Token

Access Token，顾名思义，就是用来访问受保护资源要用到的令牌。客户端要访问资源服务器上受保护的资源，就必须要有 Access Token 作为通行证。Access Token 由授权服务器生成。客户端再获取了用户授权后才能想授权服务器申请 Access Token

An access token is a string representing an authorization issued to the client. … Tokens represent specific scopes and durations of access, granted by the resource owner, and enforced by the resource server and authorization server.

从官方的定义来看（RFC 6749 #section-1.4），Access Token 是一个字符串，至少要提供关于 **客户端的基本信息（通常是客户端的 ID）** 和该客户端获得的权限，权限有一组 scopes 表示，并且 Access Token 是有有效期的

关于 Access Token 的具体格式一个字符串标识符呢，还是自包含内容的信息呢，在 OAth2（RFC6749） 中并没有规定

### Refresh Token

Refresh Token 也是有授权服务器生成，当一个 Access Token 过期或者失效时，客户端可以使用 Refresh Token 来获取一个新的 Access Token，这个新的 Access Token 拥有 scopes 范围小于等原来的那个 Access Token 权限

Refresh Token 是可选项，如果授权服务器生成了 Refresh Token，它会与 Access Token 一起返回给客户端，我们一起来看一看整个流程

![refresh-token](https://res.cloudinary.com/incoder/image/upload/v1594806547/blog/refresh-token.png)

1. （A）客户端通过向服务器镜像身份验证来请求访问令牌，授权服务器并显示授权
2. （B）授权服务器对客户端进行身份验证并验证权限授予，如果有效，则颁发访问令牌和刷新令牌
3. （C）客户端携带令牌向资源服务器发出受保护的资源请求
4. （D）资源服务器验证访问令牌，如果有效，返回受保护的资源给客户端
5. （E）重复步骤（C）和（D），直到访问令牌过期，如果客户知道访问令牌已过期，则跳至步骤（G）；否则，它将发出另一个受保护的资源请求
6. （F）由于访问令牌无效，因此资源服务器返回无效的令牌错误
7. （G）客户端通过与进行身份验证来请求新的访问令牌，授权服务器并显示刷新令牌，客户端身份验证要求基于客户端类型和授权策略
8. （H）授权服务器对客户端进行身份验证并验证刷新令牌，如果有效，则发出新的访问令牌（并且，新的刷新令牌是可选）

## OAuth2 授权模式

关于 OAuth 的授权方式，可以在`spring-security-oauth2-autoconfigure-2.1.2.RELEASE.jar` jar 文件中
*  类：`org.springframework.boot.autoconfigure.security.oauth2.authserver.OAuth2AuthorizationServerConfiguration.BaseClientDetailsConfiguration` 
*  方法：`oauth2ClientDetails()`
*  参数设置：`.setAuthrizedGrantTypes()` 中 list 包含 
   * `authorization_code`
   * `password`
   * `client_credentials`
   * `implicit` 
   * `refresh_token`
  
>由于标准的 OAuth2 协议中，授权模式并 **不包括** `refresh_token`，但在 Spring Security 的实现中将其归为一种，因此如果需要实现 `access_token`的刷新，就需要这样一种授权模式

### 授权码模式

授权码（authorization_code）模式是功能最完整，流程最严谨的授权模式。它的特点就是通过客户端的服务器与授权服务器进行交互，国内常见的第三方平台登录功能基本都是使用这种模式

![授权码模式](https://res.cloudinary.com/incoder/image/upload/v1594806818/blog/auth-code.png)

1. （A）用户访问客户端，后者将前者导向到认证服务器
2. （B）用户选择是否给予客户端授权
3. （C）假设用户给予授权，认证服务器将用户导向客户端事先指定的“重定向 URI”，同时附上一个授权码
4. （D）客户端收到授权码，附上早先的“重定向 URI”，向认证服务器申请令牌，这一步是在客户端的后台的服务器上完成，对用户不可见
5. （E）认证服务器核对了授权码和重定向 URI，确认无误后，向客户端发送访问令牌（access token）和更新令牌（refresh token）

### 密码模式

密码（password）模式是用户把账号和密码直接告诉客户端，客户端使用这些信息向授权服务器申请令牌。这需要用户对客户端高度信任，例如客户端和服务提供商是同一家公司

![密码模式](https://res.cloudinary.com/incoder/image/upload/v1594806820/blog/password.png)

1. （A）用户向客户端提供用户名和密码
2. （B）客户端将用户名和密码发给认证服务器，向后者请求令牌
3. （C）认证服务器确认无误后，向客户端提供访问令牌

### 客户端模式

客户端（client_credentials）模式是指客户端使用自己的名义而不是用户的名义向服务提供者申请权限。严格来说，客户端模式并不能算作 OAuth 协议要解决的问题的一种解决方案，但是，对于开发者而言，在一些前后端分离应用或者为移动端提供的认证授权服务器上使用的这种模式还是非常方便

![客户端模式](https://res.cloudinary.com/incoder/image/upload/v1594806819/blog/client.png)

1. （A）客户端向认证服务器进行身份认证，并要求一个访问令牌
2. （B）认证服务器确认无误后，向客户端提供访问令牌

### 简化模式

简化（implicit）模式不需要客户端服务器参与，直接在浏览器中向授权服务器申请令牌，一般若网站是纯静态页面，则可以采用这种方式

![简化模式](https://res.cloudinary.com/incoder/image/upload/v1594806820/blog/implicit.png)

1. （A）客户端将用户导向认证服务器
2. （B）用户决定是否给予客户端授权
3. （C）假设用户给予授权，认证服务器将用户导向客户端指定的“重定向 URI”，并在 URI 的 Hash 部分包含了访问令牌
4. （D）浏览器向资源服务器发出请求，其中不包括上一步收到的 Hash 值
5. （E）资源服务器返回一个网页，其中包含的代码可以获取 Hash 值中的令牌
6. （F）浏览器执行上一步获得的脚步，提取出令牌
7. （G）浏览器将令牌发给客户端

## OAuth2/OIDC 相关开源项目

* [Redhat Keycloak（Java）](http://www.keycloak.org)
* [Apereo CAS（Java）](https://www.apereo.org/project/cas)
* [IdentityServer（C#）](https://identityserver.io)
* [OpenId-Connect-Java-Spring-Server](https://github.com/mitreid-connect/OpenID-Connect-Java-Spring-Server)
* [OAuth2全家桶项目](https://github.com/newnil/oauth2-family-barrel)
* [OAuth2全家桶项目](https://github.com/monkeyk/oauth2-shiro)
* [Apache Oltu + Shiro 实现 OAuth2 服务器](https://github.com/monkeyk/oauth2-shiro)
* [Using JWT](https://www.baeldung.com/spring-security-oauth-jwt) with [Spring Security OAuth](https://github.com/Baeldung/spring-security-oauth)

## OAuth2 相关书籍

* [OAuth2 in Action](https://www.manning.com/books/oauth-2-in-action)：主要讲述 OAuth2 协议的原理知识
* [OAuth 2.0 Cookbook](https://www.packtpub.com/virtualization-and-cloud/oauth-20-cookbook)：主要讲述 OAuth2 相关实践
* [Developer Guide](https://projects.spring.io/spring-sercurity-oauth/docs/oauth2.html)

## 附录

* [OAuth 2.0 最简向导](https://medium.com/@darutk/the-simplest-guide-to-oauth-2-0-8c71bd9a15bb) 文章【需翻墙】
* [传统 Web 应用中的身份验证技术](https://insights.thoughtworks.cn/traditional-web-app-authentication/)
* [Spring Security OAuth 2.0 Roadmap Update](https://spring.io/blog/2019/11/14/spring-security-oauth-2-0-roadmap-update)
* [Okta Developer Platform](https://developer.okta.com/)
* [RFC6749 - The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749)