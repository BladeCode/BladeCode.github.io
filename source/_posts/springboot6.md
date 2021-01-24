---
title: SpringBoot（六）日志管理
date: 2020-02-22 09:03:00
categories: SpringBoot
tag: [SpringBoot]
---

日志是我们项目开发过程中必不可少的一个方面， 当我们项目引入了 `spring-boot-starter-web` 这个 jar 包，会自动引入相关的一些日志相关的 jar 包，比如，其实在项目中可供我们选择的 jar 包有很多，比如 `log4j`，`log4j2`，`logback`（现在使用最多），`slfj`等，在具体使用时并不会直接去使用`log4j`，`log4j2`而是使用`slfj`作为门面，具体的实现是通过可插拔的方式提供。`logback`实际是在`log4j`之后作者重新写的一个日志框架。本篇文章主要讲`logback`在项目中的应用

<!-- more -->

## logback-spring.xml

文件名使用 `logback-spring.xml`，位于 resource 路径下