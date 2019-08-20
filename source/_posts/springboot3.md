---
title: SpringBoot（三） JDWP远程调用
date: 2019-07-11 22:20:11
categories: SpringBoot
tag: [SpringBoot]
---

在 SpringBoot 系列的第二篇文章中，已经详细分析了 SpringBoot 的启动过程，那么这篇文章，我们通过源码调试的方式来验证我们的分析，首先我们在控制台中输入 `java` 命令，可用输出 JDK 给我们提供了一些命令，其中`-agentlib`命令就是本篇文章所介绍，用于我们进行源码调试

<!-- more -->

![springboot-java-agentlib](https://res.cloudinary.com/incoder/image/upload/v1562858657/blog/springboot-java-agentlib.png)  
我们继续查看`-agentlib`详细的命令说明，输入`java -agentlib:jdwp=help` 查看帮助文档
![springboot-java-agentlib-help](https://res.cloudinary.com/incoder/image/upload/v1562859131/blog/springboot-java-agentlib-help.png)

## 远程

```java
# 在远程机器上添加代理模式的方式启动
# 使用 socket 协议来进行远程调试，当服务启动就开始在 6666 端口等待连接
java -agentlib:jdwp=transport=dt_socket,server=y,address=6666 -jar start-1.0-SNAPSHOT.jar
```

## 本机

在本机上，我们直接使用 IDEA 编辑器，新建一个 Remote 应用服务，运行，创建步骤如下 9 步骤
![springboot-java-remote](https://res.cloudinary.com/incoder/image/upload/v1562860397/blog/springboot-java-remote.png)