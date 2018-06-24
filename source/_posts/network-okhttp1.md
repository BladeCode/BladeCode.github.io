---
title: Network（一） 之OkHttp 入门
date: 2018-06-23 12:44:25
categories: Network
tag: OkHttp
---

自从Android4.4的源码中可以看到`HttpURLConnection`已经替换成`OkHttp`开始([JakeWharton曾在Twitter表示](https://twitter.com/JakeWharton/status/482563299511250944) ) ，`OkHttp`+`Retrofit`+`RxJava`的组合网络请求一直经久不衰，主流app的网络架构基本都是这样的组合模式，存在即合理，说明`OkHttp`+`Retrofit`+`RxJava`的方式确实给开发，用户体验等带来可观的优势，那么这个系列文章围绕Android的网络展开.

OkHttp：An HTTP & HTTP/2 client for Android and Java applications

>Android 历史网络库
* `HttpClient` 是 Apache 提供的HTTP网络访问接口，从一开始的时候就被引入到了Android的API中；
* `HttpURLConnection` 是一种多用途, 轻量极的HTTP客户端, 提供的API比较简单, 可以容易地去使用和扩展. 

## OkHttp优势
* 支持HTTP/2, HTTP/2通过使用多路复用技术在一个单独的TCP连接上支持并发, 通过在一个连接上一次性发送多个请求来发送或接收数据
* 如果HTTP/2不可用, 连接池复用技术也可以极大减少延时
* 支持GZIP, 可以压缩下载体积
* 响应缓存可以直接避免重复请求
* 会从很多常用的连接问题中自动恢复
* 如果您的服务器配置了多个IP地址, 当第一个IP连接失败的时候, OkHttp会自动尝试下一个IP
* OkHttp还处理了代理服务器问题和SSL握手失败问题

## 基本使用
### OkHttp请求
### 同步请求
### 异步请求
### OkHttp响应
## OkHttp Get
## OkHttp Post
### 提交String
### 提交流
### 提交文件
### 提交表单
### 提交分块请求
## 其他用法
### 提取响应头
### Gson解析JSON响应
### 响应缓存
### 取消一个Call
### 超时
### 每个Call的配置
### 处理验证

