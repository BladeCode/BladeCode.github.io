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

## 编译构建

使用 IDEA 打开项目，会自动创建索引以及，下载项目的依赖，由于依赖的 jar 比较多，建议使用 [阿里云](https://maven.aliyun.com/) 镜像，关于 Gradle 怎么修改依赖镜像源，可参考 [专治各种网络不服](https://incoder.org/2020/02/27/fuck-gfw/#Gradle) 文章，阿里云镜像能加速大部分的 jar，但有一部分在阿里云上并没有，你可以通过手动方式导入到本地