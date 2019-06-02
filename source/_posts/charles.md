---
title: Charles 使用教程
date: 2018-11-29 11:25:46
categories: DevTool
tag: [Charles]
---

![charles](https://res.cloudinary.com/incoder/image/upload/v1559463393/blog/charles.png)

Charles is an HTTP proxy / HTTP monitor / Reverse Proxy that enables a developer to view all of the HTTP and SSL / HTTPS traffic between their machine and the Internet. This includes requests, responses and the HTTP headers (which contain the cookies and caching information)

<!-- more -->

Charles是一个HTTP代理/ HTTP监视器/ 反向代理，使开发人员能够查看其机器和Internet之间的所有HTTP和SSL / HTTPS流量，这包括请求，响应和HTTP标头（包含cookie和缓存信息）

## Charles
主要特点
* [SSL代理](https://www.charlesproxy.com/documentation/proxying/ssl-proxying/) - 以纯文本格式查看SSL请求和响应
* [Bandwidth Throttling](https://www.charlesproxy.com/documentation/proxying/throttling/)模拟较慢的Internet连接，包括延迟
* AJAX调试 - 以树或文本形式查看XML和JSON请求和响应
* [AMF](https://www.charlesproxy.com/documentation/additional/amf/) - 以树形式查看Flash Remoting / Flex Remoting消息的内容
* 重复请求以测试后端更改
* 编辑测试不同输入的请求
* 用于拦截和编辑请求或响应的断点
* 使用W3C验证器验证记录的HTML，CSS和RSS / atom响应

>本篇文章操作均基于Charlers 4.2.8版本，及 macOS 10.14.5 版本

### 安装

* Windows：略
* macOS：略
>下载地址：[官方Charles](https://www.charlesproxy.com/download/)

#### 激活

<font color=#FF0000>有能力，请支持付费支持正版~</font>  
<font color=#FF0000>有能力，请支持付费支持正版~</font>  
<font color=#FF0000>有能力，请支持付费支持正版~</font>  

仅供个人学习研究和交流使用，请勿用于任何商业用途。
Charles ——> Help ——> Register Charles...
```
Registered Name: https://zhile.io
License Key: 48891cf209c6d32bf4
```
[激活密钥来自CSDN](https://blog.csdn.net/qq_25821067/article/details/79848589)

### 配置
配置流程
1. 获取操作系统网络IP地址
2. 修改客户端网络IP连接
3. 在操作系统及客户端上安装证书
4. 设置SSL代理
> 以上配置要求，操作系统（Windows，macOS）及客户端（Android，iOS）连接在 **同一WiFi网络**

#### 获取系统 IP
不管是是 Windows 系统还是 macOS 系统都可以通过 Charles 来获取，获取方式 `Help` ——> `Local IP Address`  
![charles-ip](https://res.cloudinary.com/incoder/image/upload/v1559436487/blog/charles-ip.png)

##### Windows
使用命令行查看网络 IP 地址 `ipconfig`
![charles-windows-ip](https://res.cloudinary.com/incoder/image/upload/v1559437724/blog/charles-windows-ip.png)

##### macOS
* 使用命令行查看网络 IP 地址 `ifconfig en0`
    ![charles-mac-ip](https://res.cloudinary.com/incoder/image/upload/v1559436484/blog/charles-mac-ip.png)
* macOS系统设置查看IP 地址 `System Preferences` ——> `Network`
    ![charles-network-ip](https://res.cloudinary.com/incoder/image/upload/v1559437653/blog/charles-network-ip.png)

#### 查看监听端口
`Proxy`——> `Proxy settings...`
![charles-view-port](https://res.cloudinary.com/incoder/image/upload/v1559437270/blog/charles-view-port.png)

#### 客户端设置
要求手机网络与 PC 网络同链接在一个路由器网络下，这样手机的请求都将通过 PC，因此在 Charles 上可以看到手机上的网络请求。

手机上安装下面的步骤请看下面的详细介绍，安装完证书，Charles 将会收到提示，进行允许即可  
![charles-allow](https://res.cloudinary.com/incoder/image/upload/v1559465134/blog/charles-allow.png)

##### Android
* 修改网络配置选项

* 导入Charles证书，使用浏览器打开 [www.charlesproxy.com/getssl](http://www.charlesproxy.com/getssl) 或 [http://chls.pro/ssl](chls.pro/ssl)，下载证书，并进行安装

##### iOS
* 修改网络配置选项

* 导入Charles证书

* 证书授权

##### 模拟器

#### Charles设置

* Install Charles Root Certificate
完成客户端的设置，我们此时再对 Charles 进行设置，首先我们先进性安装 Charles 证书，`Help` ——> `SSL Proxying...` ——> `Install Charles Root Certificate`  
    ![charles-install-system](https://res.cloudinary.com/incoder/image/upload/v1559464456/blog/charles-install-system.png)
* 添加证书
    ![charles-add](https://res.cloudinary.com/incoder/image/upload/v1559464726/blog/charles-add.png)
* 证书授权设置
    ![charles-ca-settings](https://res.cloudinary.com/incoder/image/upload/v1559463067/blog/charles-ca-settings.png)
* SSL Proxy settings
    ![charles-ssl-settings](https://res.cloudinary.com/incoder/image/upload/v1559463076/blog/charles-ssl-settings.png)
    * Host：为需要过滤的域名地址，`*` 表示不过滤
    * Port：固定为443，`*` 表示任意端口

## 抓包
不废话，请看图
![charles-overview](https://res.cloudinary.com/incoder/image/upload/v1559467975/blog/charles-overview.png)

* Structure：视图将网络请求按访问的域名分类
* Sequence：视图将网络请求按访问的时间排序

> 客户端请不要开启其他代理

## 进阶

### 过滤网络请求
* 方法一：在上面抓包的截图中，已经讲过，适用于 **临时型** 对请求进行过滤
* 方法二：`Proxy` ——> `Recording settings` ——> `include` ，适用于 **经常性** 请求过滤
    ![charles-filter-often](https://res.cloudinary.com/incoder/image/upload/v1559470804/blog/charles-filter-often.png)

### Map 功能

#### 设置本地映射
指的是将网络请求重定向到本地的文件，适用于开发过程中，把线上的静态资源映射到本地，这样可以方便调试并及时查看效果，确定无误后再发布到线上环境
![charles-map-local](https://res.cloudinary.com/incoder/image/upload/v1559473299/blog/charles-map-local.png)

#### 设置远程映射
指的是将网络请求重定向到另一个网络请求地址，适用于开发过程中，需要将请求重定向到其他的服务上
![charles-map-remote](https://res.cloudinary.com/incoder/image/upload/v1559473845/blog/charles-map-remote.png)

### 重复发送网络请求
可以更加需要重复一次或多次的请求，对于多次的请求可用于服务器的压力测试
![charles-repeat](https://res.cloudinary.com/incoder/image/upload/v1559475397/blog/charles-repeat.png)

## 常见问题
1. Charles无法抓取到客户端网络请求
    * 查看你的客户端网络设置，是否正确
    * 查看你的客户端和 Charles 是否是处于同一网络环境
2. Charles 无法抓取 Https 网络请求
    * 查看你的客户端和 Charles 是否安装证书，并设置为终是允许

3. 网络请求及网络响应信息中文乱码

## 附录
* [Charles Document](https://www.charlesproxy.com/documentation)
* [Charles抓包的安装，使用说明以及常见问题解决](https://blog.csdn.net/zhangxiang_1102/article/details/77855548)
* [抓包工具Charles的使用心得](https://www.jianshu.com/p/fdd7c681929c)
* [Charles抓包https](https://www.jianshu.com/p/ec0a38d9a8cf)