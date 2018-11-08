---
title: Fiddler 初体验
date: 2018-10-25 19:40:46
categories: DevTool
tag: [Fiddler]
---

在开发的路上，有时候面对一些应用，我们可能回去分析研究它的实现以及数据交互等，在没有官方没有公开的Api提供时，我们会用到一项实用的技术，抓包，所谓的抓包，指的是截取网络传输发送与接收的数据包。其中在Windows平台上使用比较广泛的要数[Fiddler](https://www.telerik.com/fiddler)

本节主要讲解Fiddler的相关配置及简单使用

## 资源
* Windows 10
* Fiddler

## 配置
需要使手机连接WiFi和电脑WiFi是使用同一个网络
### Fiddler 初始化
![fiddler-config](https://res.cloudinary.com/incoder/image/upload/v1540742306/blog/fiddler-config.png)
>默认端口：8888

### 网络地址
获取电脑所连接的网络IP地址
![fiddler-ip](https://res.cloudinary.com/incoder/image/upload/v1540742723/blog/fiddler-ip.png)
这里获取的IP地址，将用于手机连接网络的代理

### 手机配置
关于手机相关的配置操作，步骤已经通过下面的视频展现。
1. 连接与电脑相同的WiFi
2. 修改网络代理
3. 手动模式，并设置电脑端获取的IP地址及Fiddler默认端口号8888
4. 网络连接刷新
5. 获取并下载安装Fiddler证书

{% dplayer "url=https://res.cloudinary.com/incoder/video/upload/v1540740276/blog/video/fiddler-content.mp4" "pic=https://res.cloudinary.com/incoder/image/upload/v1540743876/blog/fiddler.jpg" "loop=no" "theme=#FADFA3" "autoplay=false" "token=tokendemo" "width=350px" "height=680px" %}

## 其它
通过以上操作，现在可以在电脑端Fiddler工具中，拦截获取经过的所有网络信息。而我们一般是查看或者是分析某一款应用的数据信息，这样在查看起来就比较费力，那么我们就借助Fiddler提供的过滤功能
![fiddler-filter](https://res.cloudinary.com/incoder/image/upload/v1540743106/blog/fiddler-filter.png)

选择过滤方式中
1. 第一项有三个选项，不做更改：
    “No zone filter”;
    “Show Only Intranet Hosts”;
    “Show Only Internet Hosts”
2. 第二个选项是只监控以下网址，如只监控百度，在下面的输入框里填上 www.baidu.com
    “No Host Filter”：不设置hosts过滤
    “Hide The Following Hosts”：隐藏过滤到的域名
    “Show Only The Following Hosts”：只显示过滤到的域名
    “Flag The Following Hosts”：标记过滤到的域名
3. 文本框内输入需要过滤的域名，多个域名使用”;“分号分割。
>fiddler默认会检查http头中设置的host，强制显示http地址中域名。