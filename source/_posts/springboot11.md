---
title: SpringBoot 源码构建
date: 2020-12-31 10:24:00
categories: SpringBoot
tag: [SpringBoot, Gradle]
---

前两天刚刚学习了 Gradle 构建 SpringBoot 项目，再查看官方文档时，得知 SpringBoot 从 [Spring Boot 2.3.0.M1](https://spring.io/blog/2020/06/08/migrating-spring-boot-s-build-to-gradle) 版本开始完全切换到使用 Gradle 来构建项目，那么本篇文章就来实践，基于源码来编译构建 SpringBoot，话不多说，本次构建构建是 2020 年的最后一次发布的版本 2.4.1

<!-- more -->

## 环境

* OS：macOS 11.1
* JDK：JDK1.8
* Gradle：6.7.1-bin
* IDE：IntelliJ IDEA Community 2020.3

Gradle 版本通过 https://github.com/spring-projects/spring-boot/blob/master/gradle/wrapper/gradle-wrapper.properties 文件可知，使用的 6.7.1-bin，那么本地也使用该版本编译，对于 Gradle 的安装可参考 [Gradle（一）基础](https://incoder.org/2020/12/10/gradle1/#Gradle-安装配置) 文章

## 获取源码

```bash
# 这里使用 cnpmjs 来提高 clone 速度
git clone https://github.com.cnpmjs.org/spring-projects/spring-boot.git
```


~/.gradle/caches/modules-2/files-2.1/org.jetbrains.kotlin/kotlin-compiler-embeddable/1.4.21 ❯


<!-- more -->
