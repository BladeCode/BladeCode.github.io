---
title: SpringBoot（一） 初识
date: 2019-06-23 09:10:11
categories: SpringBoot
tag: [SpringBoot]
---

![springboot](https://res.cloudinary.com/incoder/image/upload/v1561900597/blog/springboot.jpg)

从本篇文章开始，记录学习 SpringBoot框架在实践，源码方面的知识，本节是第一篇，因此不涉及相关复杂知识的学习。众所周知，随着微服务的广泛流行，Spring 系列的 SpringBoot 和 SpringCloud 的应用也更受欢迎，那么请跟随我的脚本来一步步解开 SpringBoot 她神秘的面纱

<!-- more -->

熟悉后端服务开发的小伙伴，在使用 SpringBoot 时一定会有这样的感受，咦，以前繁琐的配置，现在都不用再去配置一大堆东西了，以前跑起来一个 demo，感觉真是千辛万苦，错一步就 game over，以前服务基本都是已 war 包的形式运行在 Tomcat 中，而现在，你基本不需要手动写太多的代码，一个应用服务就可以运行起来，其次现在应用基本已 jar 包方式直接运行，虽然本质还是运行在 Tomcat 中，但现在 jar 包中已经有了服务运行的基础环境，可以直接使用 jar 相关的运行命令就可以运行起服务。好了，废话了这么多，先看看我们如何运行起一个 DEMO 应用。

## 环境及版本
* SpringBoot Version：2.1.6.RELEASE
* System：macOS Mojave
* JDK Version：1.8
* Gradle：5.4.1
* IDE：IntelliJ IDEA

>本系列应用使用如上环境，其次应用包管理，小伙伴可以选择自己熟悉的 Maven 进行管理，而这里都使用 Gradle 进行管理

## Demo

### [Spring Initializr](https://start.spring.io)

为了让开发者快速上手，官方提供了一建生成 SpringBoot 项目，你按需选择你需要的依赖即可。操作步骤如下截图
![spring-initializ](https://res.cloudinary.com/incoder/image/upload/v1561906733/blog/spring-initializr.png)

### IDEA Init


## jar 分析

![springboot-deploy-jar-unzip](https://res.cloudinary.com/incoder/image/upload/v1561259381/blog/springboot-deploy-jar-unzip.png)

目录说明
```
project/
├── BOOT-INF/
│   ├── classes
│   │   │   └── application.properties
│   │   ├── org/
│   │   ├── static/
│   │   └── templates/
│   └── lib
├── META-INF/
│   └── MANIFEST.MF
└── org/springframework/boot/loader/
    ├── archive/
    ├── data
    ├── ExectableArchiveLauncher.class
    ├── jar/
    ├── JarLauncher.class
    ├── LaunchedURLClassLoader.class
    ├── LaunchedURLClassLoader$UseFastConnectionExceptionsEnumeration.class
    ├── Launcher.class
    ├── MainMethodRunner.class
    ├── PropertiesLauncher.class
    ├── PropertiesLauncher$1.class
    ├── PropertiesLauncher$ArchiveEntryFilter.class
    ├── PropertiesLauncher$PrefixMatchingArchiveFilter.class
    ├── PropertiesLauncher$ArchiveEntryFilter.class
    ├── util/
    └── WarLauncher.class
```