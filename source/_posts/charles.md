<<<<<<< HEAD
---
title: Charles 使用教程
date: 2018-11-29 11:25:46
categories: DevTool
tag: [Charles]
---

Charles is an HTTP proxy / HTTP monitor / Reverse Proxy that enables a developer to view all of the HTTP and SSL / HTTPS traffic between their machine and the Internet. This includes requests, responses and the HTTP headers (which contain the cookies and caching information)

Charles是一个HTTP代理/ HTTP监视器/反向代理，使开发人员能够查看其机器和Internet之间的所有HTTP和SSL / HTTPS流量，这包括请求，响应和HTTP标头（包含cookie和缓存信息）

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

### 安装
* 下载地址：[官方Charles](https://www.charlesproxy.com/download/)
* Windows：略
* macOS：略

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
> 以上配置要求，操作系统（Windows，macOS）及客户端（Android，iOS）连接在同一WiFi网络

#### Windows
* 获取当前系统IP地址
    * 方式一

    * 方式二

* 查看Charles监听端口

* Charles证书安装

#### macOS

#### Android
* 修改网络配置选项

* 导入Charles证书

#### iOS
* 修改网络配置选项

* 导入Charles证书

* 证书授权

#### 模拟器


## 抓包
以下操作均基于Charlers 4.2.7版本

> 客户端请不要开启其他代理

## 进阶

### 过滤网络请求

### 设置映射
#### 设置本地映射

#### 设置远程映射

#### 重复发送网络请求

#### 修改网络请求参数

#### 网络请求的接获并动态修改

## 常见问题
1. Charles无法抓取到客户端网络请求

2. 网络请求及网络响应信息中文乱码

## 附录
* [Charles Document](https://www.charlesproxy.com/documentation)
* [Charles抓包的安装，使用说明以及常见问题解决](https://blog.csdn.net/zhangxiang_1102/article/details/77855548)
* [抓包工具Charles的使用心得](https://www.jianshu.com/p/fdd7c681929c)
=======
---
title: Charles 使用教程
date: 2018-11-29 11:25:46
categories: DevTool
tag: [Charles]
---

Charles is an HTTP proxy / HTTP monitor / Reverse Proxy that enables a developer to view all of the HTTP and SSL / HTTPS traffic between their machine and the Internet. This includes requests, responses and the HTTP headers (which contain the cookies and caching information)

Charles是一个HTTP代理/ HTTP监视器/反向代理，使开发人员能够查看其机器和Internet之间的所有HTTP和SSL / HTTPS流量，这包括请求，响应和HTTP标头（包含cookie和缓存信息）

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

### 安装
* 下载地址：[官方Charles](https://www.charlesproxy.com/download/)
* Windows：略
* macOS：略

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
> 以上配置要求，操作系统（Windows，macOS）及客户端（Android，iOS）连接在同一WiFi网络

#### Windows
* 获取当前系统IP地址
    * 方式一

    * 方式二

* 查看Charles监听端口

* Charles证书安装

#### macOS

#### Android
* 修改网络配置选项

* 导入Charles证书

#### iOS
* 修改网络配置选项

* 导入Charles证书

* 证书授权

#### 模拟器


## 抓包
以下操作均基于Charlers 4.2.7版本

> 客户端请不要开启其他代理

## 进阶

### 过滤网络请求

### 设置映射
#### 设置本地映射

#### 设置远程映射

#### 重复发送网络请求

#### 修改网络请求参数

#### 网络请求的接获并动态修改

## 常见问题
1. Charles无法抓取到客户端网络请求

2. 网络请求及网络响应信息中文乱码

## 附录
* [Charles Document](https://www.charlesproxy.com/documentation)
* [Charles抓包的安装，使用说明以及常见问题解决](https://blog.csdn.net/zhangxiang_1102/article/details/77855548)
* [抓包工具Charles的使用心得](https://www.jianshu.com/p/fdd7c681929c)
>>>>>>> 5611ab4da68955d1de51bf13cc4ae75b06c83d33
* [Charles抓包https](https://www.jianshu.com/p/ec0a38d9a8cf)