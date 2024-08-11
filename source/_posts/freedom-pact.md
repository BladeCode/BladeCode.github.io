---
title: 评论不自由，赞美无意义
date: 2021-05-05 12:30:10
categories: Agent
tag: [VPN]
---

就像文章标题所述。每到三，五月这个时间，网络变得异常脆弱。各种“奇怪”的网站访问起来很费劲。对于一个技术人员，这些问题可以解决，但是每次都需要花费一定的时间和精力来应对这些，而且随着 [GFW](https://baike.baidu.com/item/GWF) 的不断升级和加强，应对的策略和技术也是在不断迭代，前前后后已经出现了多种技术，本篇就以这个契机，梳理截止到 2021-05-05 所了解到关于 “代理” 相关的知识。现在主流的科学上网技术有 VPN、SS、SSR、V2Ray、Trojan、Trojan-Go，小众的 WireGuard、Brook、Snell 和 NaiveProxy 等

<!-- more -->

{% note info %}
本篇文章大量使用了 [一灯不是和尚](https://iyideng.net/about/myhome.html) 作者发布 《[科学上网工具哪个好？](https://iyideng.net/black-technology/cgfw/vpn-ss-ssr-v2ray-trojan-wireguard-bypass-gfw.html) 》文章中的内容，在此感谢作者对各技术的汇总以及经验总结，本篇文章是在原文章的基础上进行的扩展补充
{% endnote %}

## VPN

[虚拟专用网络](https://zh.wikipedia.org/wiki/%E8%99%9B%E6%93%AC%E7%A7%81%E4%BA%BA%E7%B6%B2%E8%B7%AF)（Virtual Private Network，缩写：VPN）是常用于连接中，大型企业或团体间私人网络的通讯方法。它利用隧道协议（Tunneling Protocol）来达到发送端认证，消息保密与准确性等功能。

VPN 只是一个统称，它有多种具体实现。比如：

* PPTP（点对点隧道协议：Point to Point Tunneling Protocol）;
* L2TP（第二层隧道协议：Layer Two Tunneling Protocol）;
* IPsec（互联网安全协议：Internet Protocol Security）;
* WireGuard（一种协议）;
* OpenVPN（一种虚拟专用网络 **V**irtual **P**rivate **N**etwork（VPN）系统）;
* IKEv2（因特网密钥交换：Internet Key Exchange） 等

### WireGuard

WireGuard 是由 Jason A. Donenfeld 开发，是最新的协议实现（在 2020 年，WireGuard 协议已被添加到 Linux 和 Android 内核中，从而为 VPN 提供商所采用。默认情况下，WireGuard 使用 Curve25519 进行秘钥交换，并使用 ChaCha20 进行加密，但还具有客户端和服务器之间预共享对称秘钥的功能）

### OpenVPN

OpenVPN 是由 James Yonan 编写，实现了在路由或桥接配置和远程访问设施中创建安全点对点或站点对站点连接的技术。它实现了客户端和服务器应用程序。它不与IPsec兼容

OpenVPN 允许对等方使用预先共享的密钥、证书或用户名/密码相互验证。当在多客户端服务器配置中使用时，它允许服务器使用签名和证书颁发机构为每个客户端发布身份验证证书。

## SS

SS 是 [Shadowsocks](https://github.com/shadowsocks) 的缩写，中文名为影梭，为了避免关键词过滤，网友喜欢将 Shadowsocks 称为 “酸酸”，是一种基于 Socks5 代理方式的加密传输协议，也可以指定实现这个协议的各种开发包。Shadowsocks 是由 [Clowwindy](https://github.com/clowwindy) 为了自己使用谷歌查资料而编写；Shadowsocks 分为服务端和客户端，在使用之前，需要先将服务器端程序部署到服务器上面，然后通过客户端连接并创建本地代理。后来，他觉得这个东西非常好用，速度也很快，于是将源码提交到了 GitHub。由于其优秀的使用体验，Shadowsocks 被广泛传播，导致作者被某部门请去 “喝茶”。迫于压力 Clowwindy 于 2015-08-22 宣布停止维护此项目，并移除其个人页面所存储的源代码，而且保证永不再参与维护更新

虽然 Clowwindy 被迫放弃了 Shadowsocks，但开源界没有放弃，各路大神依旧在为 Shadowsocks 添砖加瓦，这就是开源的力量，倒下一个 Clowwindy，会有千千万万个 "Clowwindy" 站出来

## SSR

SSR 是 ShadowsocksR 的缩写，网名爱称 “酸酸乳”，是在 Shadowsocks 的作者被请去喝茶之后，网名为 [breakwa11](https://github.com/breakwa11) 的用户发起的 Shadowsocks 的一个分支版本，它在 Shadowsocks 的基础上增加了一些数据混淆方式，修复了部分安全问题并提高了 QoS 优先级。由于 ShadowsocksR 在协议和混淆方面做了改进，更加不容易被 GFW 检测到，而且兼容原 Shadowsocks，并为新项目命名为 Shadowsocks-R，一开始部分代码由社区人员进行更新。由于不完全开源，也导致后来使用 SS 和 SSR 的用户分为两个阵营，互相撕逼，直到开发者 breakwa11 被人肉出来。breakwa11 最终决定删除 Shadowsocks-R 项目的所有代码，并解散了所有相关群组

### 事件始末澄清

ShadowsocksR 的作者一开始曾有过违反 GPL 协议，在发布二进制文件时不开放源码的争议。不过后来 Shadowsocks-R 项目由 breakwa11 采用了与 Shadowsocks 相同的 GPL、Apache、MIT 等多重自由软件许可协议

* 2017-07-19，ShadowsocksR 作者 breakwa11 在 Telegram 频道 ShadowsocksR news 里转发了深圳市启动 SS 协议检测的消息并被大量用户转发，在电报（TG）圈引发恐慌。  
* 2017-07-24，breakwa11 遭到自称 “ESU.TV” 的不明省份人士人身攻击，对方宣传如果不停止开发并阻止用户讨论此事件将发布更多包含个人隐私的资料，随后 breakwa11 表示遭到对方人肉搜索并公开个人资料。为防止对方继续伤害无关人士，breakwa11 删除了 GitHub 上的所有代码、解散相关交流群组，并停止 ShadowsocksR 项目

从本质上来说，Shadowsocks 与 ShadowsocksR 的基本原理相同，都是基于 Socks5 的代理工具，只在本地客户端和服务器对数据包加解密，然后使用 Socks5 协议转发加密的数据包，而不用在乎使用什么协议，所以 Socks5 代理比其他应用层代理速度要快的多

### Socks5

这里顺带科普一下 Socks5，Socks5 代理的原理是把你的网络数据请求先发送到你的代理服务器，然后由代理服务器转发给目标；如果目标有反馈发送到代理服务器，那么代理服务器会将数据包直接传回到你的本地网络，整个过程只需要数据的二次传输，并没有额外的处理。

> 示例：现在呢在深圳，你的代理服务器在香港，如果你想要访问 Google，那么你首先需要把数据请求通过本地 Socks5 代理客户端发给在你在香港的服务器上的 Socks5 代理服务端，然后你在香港的服务器将数据请求发送给 Google，再把 Google 反馈的结果传到你香港的代理服务器，然后通过 Socks5 服务端回传到本地的 Socks5 客户端，这样就可以绕开 GFW 的检测而实现科学上网

显而易见，Socks5 代理的所有数据走的任然是公网，而且在公网传输过程中，没有对数据进行任何加密和混淆，这跟 VPN 在公网建立虚拟专用通道传输过程中，对数据高强度加密的方式完全不同。Shadowsocks 和 ShadowsocksR 只在客户端和服务器端对数据做了简单加密和认证，主要功能是流量转发，过强才是主要目的。虽然 ShadowsocksR 已经停止更新很久了，而 Shadowsocks 仍处于社区人员的更新和维护之中，不断修复漏洞并增加新功能，所以现在 Shadowsocks 比 ShadowsocksR 更强大

### 提醒

不要迷信 SSR 一定比 SS 强，也包括现在的 V2Ray，Trojan，甚至是 WireGuard 等，因为增加混淆意味着损失速度，混淆加密越是强悍，那么其速度和稳定性损失就越大，另外 SSR 至今已被研究透了，而且长时间没有更新维护，其流量特征是可以被 GFW 精准识别，所以用 SSR 和 SS 没有本质区别，由于 SS 一直更新维护，反而更稳定。

## V2Ray

[V2Ray](https://v2fly.org) 是在 Shadowsocks 被封杀后，为表示抗议而开发，属于后起之秀，功能更加强大，为抗 GFW 封锁而生。V2Ray 现在已经是 Project V 项目的核心工具。而 Project V 是一个平台，其中也包括支持 Shadowsocks 协议。由于 V2Ray 早于 Project V 项目，且名声更大，我们习惯称 Project V 项目为 V2Ray，所以我们平常所说的 V2Ray 其实就是 Project V 这个平台，也就是一个工具集。其中，只有 VMess 协议是 V2Ray 社区原创的专属加密通讯协议

V2Ray 目前支持一下协议（截止 2019-12）

* Blackhole: 中文名称“黑洞”，是一个出站数据协议，它会阻碍所有的数据的出站，配合路由（Routing）一起使用，可以访问被封杀的网站
* DNS: 是一个出站协议，主要用于拦截和转发 DNS 查询。此出站协议只能接收 DNS 流量（包含基于 UDP 和 TCP 协议的查询），其它类型的流量会导致错误。在处理 DNS 查询时，此出站协议会将 IP 查询（即 A 和 AAAA）转发给内置的 DNS 服务器。其它类型的查询流量将被转发至原本的目标地址，DNS 出站协议在 V2Ray 4.15 中引入
* Dokodemo-door: 中文名称“任意门”，是一个入站数据协议，它可以监听一个本地端口，并把所有进入此端口的数据发送到指定服务器的一个端口，从而达到端口映射的效果
* Freedom: 是一个出站协议，可以用来向任意网络发起（正常的）TCP 或 UDP 数据
* HTTP: 超文本传输协议，是传统的代理协议
* Socks: 标准的 Socks 协议实现，兼容 [Socks 4](http://ftp.icm.edu.pl/packages/socks/socks4/SOCKS4.protocol)、[Socks 4a](http://ftp.icm.edu.pl/packages/socks/socks4/SOCKS4A.protocol)、[Socks 5](http://www.socks.nec.com)『🙃目前无法正常访问』，也属于是一种传统的代理协议
* VMess: 是 V2Ray 专用的加密传输协议，它分为入站和出站两部分，通常作为 V2Ray 客户端和服务器之间的桥梁。因为增加了混淆和加密，据说比 Shadowsocks 更安全。VMess 依赖于系统时间，请确保使用 V2Ray 的系统 UTC 时间误差在 90 秒之内，与时区无关。在 Linux 系统中可以安装 ntp 服务来自动同步系统时间
* Shadowsocks: 包含入站，出站两部分协议，兼容大部分其它版本的实现。最早被个人开发的科学上网梯子协议，但 V2Ray 目前不支持 ShadowsocksR
* [Trojan](https://trojan-gfw.github.io/trojan/protocol): 特洛伊木马服务器如何对有效的特洛伊木马协议和其他协议（可能是 HTTPS 或任何其他探针）做出反应
* VLESS: 是一个无状态的轻量传输协议，它分为入站和出站两部分，可以作为 V2Ray 客户端和服务器之间的桥梁，与 VMess 不同，VLESS 不依赖与系统时间，认证方式同样为 UUID，但不需要 alterId
* Loopback: 是一个出站协议，可使出站连接被重新路由，最低支持版本 v4.36.0+

截止到 2021-05，V2Ray 可选的传输层配置有：TCP、mKCP，WebSocket、HTTP/2，DomainSocket、QUIC、gRPC。其中 mKCP、QUIC 和 TCP 用于优化网络质量；WebSocket 用于伪装；HTTP/2 和 DomainSocket 用于传输以及 TLS 加密

V2Ray 不仅可以在传输层配置 TLS 使用 HTTP 和 Socks 变成 HTTPS 和 Socks over TLS 协议，也可以使用 MTProto、Shadowsocks 和 VMess 通过传输层配置 TLS 加密伪装成 TLS 流量。所以，VMess 配置是 TLS 加密的最常见的做法，但没人会对 Shadowsocks 使用 TLS 加密，因为完全没有意义

### 客户端

> [V2Ray 客户端](https://v2xtls.org/v2ray%e5%ae%a2%e6%88%b7%e7%ab%af/)

## Xray 与 XTLS

Xray 与 V2Ray 完全类同，Xray 是 Project X 项目的核心模块。因为 Xray 和 [XTLS 黑科技](https://github.com/XTLS/) 的作者 [rpfx](https://github.com/rprx) 曾是 V2fly 社区的重要成员，所以 Xray 直接 fork 全部 V2Ray 的功能，然后进行性能优化，并增加了新的功能，使 Xray 在功能上成为了 V2Ray 的超集，且完全兼容 V2Ray。

简而言之，Xray 是 V2Ray 的项目分支，Xray 是 V2Ray 的超集，就跟 Trojan-Go 和 Trojan-GFW 的关系类似，而且 Xray 性能更好，速度更快，更新迭代也更频繁。

> 由于 V2Ray-core 4.33.0 版本起，删除了 XTLS 黑科技，但任然支持 VLESS，所以是否原生支持 XTLS 是 Xray 和 V2Ray 最大的区别之一

## Trojan 与 Trojan-Go

Trojan 原特指特洛伊木马，是一种计算机病毒程序。但是，我们今天所说的 Trojan 是一种新型的科学上网技术，全称为 Trojan-GFW，是目前最成功的科学上网伪装技术之一。你可以认为 Trojan 是 V2Ray 的 “WS + TLS” 模式的精简版，速度比 V2Ray 更快，伪装比 V2Ray 更逼真，更难以备 GFW 识别

Trojan 工作原理：Trojan 通过监听 443 端口，模仿互联网上最常见的 HTTPS 协议，把合法的 Trojan 数据伪装成正常的 HTTPS 通信，并真正地完整完成 TLS 握手，以诱骗 GFW 认为它就是 HTTPS，从而不被识别。Trojan 处理来自外界的 HTTPS 请求，如果是合法的，那么为该请求提供服务，否则将该流量交给 Caddy、Nginx 等 Web 服务器，由 Caddy、Nginx 等为其提供网页访问服务。基于整个交互过程，这样能让你的 VPS 更像一个正常的 Web 服务器，因为 Trojan 的所有行为均为 Caddy、Nginx 等 Web 服务器一致，并没有引入额外特征，从而达到难以识别的效果

Trojan-Go 是 Trojan-GFW 的分支项目，对 Trojan 进行性能优化，并增加不少新特性，Trojan-Go 性能和功能均有大幅度的提升，而且支持分流和 CDN

## 总结

通过上述，以及其他信息来源对这些技术有了一定的基本认识，从以下的几个方面来进行总结

### 原理不同

VPN：强调对公网传输过程中数据的加解密
SS/SSR/V2Ray/Xray/Traojan：专注于在客户端和服务器间加密，公网传输过程中特征没有 VPN 明显

### 目的不同

VPN：是走在公网中自建的虚拟专用通道，使用强大的加解密算法，为数据传输安全性、私密性而生，被广泛应用于企业、高校、科研部门等远程数据传输领域
SS/SSR/V2Ray/Xray/Trojan/Trojan-Go：是为了数据能够安全通过 GFW 而生，更强调的是对数据的混淆和伪装，加解密只是为了更好的隐藏数据特征而顺利通过 GFW 的检测

### 项目诞生的大致顺序

VPN > SS > SSR/V2Ray/WireGuard > Trojan/Trojan-Go > Xray

## 致每一个追求真理的人

凡事都有两面性，看如何去看待。其实我一直认为，有墙确实是一件好事，毕竟祖国互联网发展也不过 20 多年，在一定程度上过滤掉了一些没有自我认知，自我思考盲目跟风，觉得国外的月亮圆，国外什么都好的一群人；保障了在互联网开始萌芽的本土企业（毕竟国外的产品和技术都已经发展了好几轮，从解决问题的能力，使用的用户体验（不含本地化）等等方面都是完全甩开本地企业的服务）发展，提供给企业和网民一起成长的安全环境，在这一点上 GFW 功不可没。但作为一个技术从业人员来说，其中大部分的技术理念思想，技术平台等纯技术领域相关的东西来说不是那么友好，常常伴随着由于网络原因而造成的各种乱七八糟的问题，对于暂无本土相关替代服务支持时，在一定程度上阻碍了国内相关技术的发展进度和创新。

真如前面所述，凡事都有两面性。对于未知的事物，人类本能的会产生恐惧，因为它不可控，而互联网正真不可控的不是技术而是人，人是复杂的个体，一个技术的好坏不是它本身，而是使用的人在做什么样的事，而做的事在一定的环境下它是有好坏之分的。因此我在这里对自我约束，仅为获取相关学习的知识，不参与散布谣言、政治相关等言论，踏踏实实搞技术

> 希望未来的有一天，国内技术人员的输出是全球技术的风向标

## 参考

1. [试用 Always Free 云服务](https://www.oracle.com/cn/cloud/free/)
2. [申请 Oracle Cloud 永久免费服务](https://www.daniao.org/6537.html)
3. [2021年申请永久免费甲骨文云 Oracle Cloud 并创建实例最全攻略](https://zhuanlan.zhihu.com/p/352736372)
4. [多种科学上网指导教程汇总](https://merlinblog.xyz/wikipageguide.html)
5. [V2ray XTLS黑科技](https://v2xtls.org)
