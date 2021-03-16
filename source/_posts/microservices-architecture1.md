---
title: 微服务架构 - 阿里系生态整合（一）
date: 2020-11-11 07:10:00
categories: Microservices
tag: [Microservices, Alibaba]
---

曾几何时，市面上对于微服务，分两个派系，一个派系以阿里为主的 Dubbo 生态体系，还有一派以 Spring Cloud 生态为主的体系，这两个系列的讨论也一直没有停息过。但现在 Spring Cloud Alibaba 的出现，提供了一整套构建分布式应用开发的微服务组件，由于这些组件是构建在原生的 Spring Cloud 之上，因此其服务治理方面的能力可认为是 Spring Cloud Plus， 不仅完全覆盖 Spring Cloud 原生特性，而且提供更为稳定和成熟的实现。那么从本系列就开始跟着我一起用阿里系的应用搭建分布式微服务应用，满足企业级的应用需要，而不是停留在 Dome 级别的应用框架使用。废话不多说，我们一起开始这一系列的实践

<!-- more -->

本篇文章主要讲一讲在构建分布式微服务应用时，经常遇到的问题以及对于同类型组件选择，以及在开发过程中相关问题的思考，对于在整个应用开发过程中，开发人员应该怎么去配合等等，那第一个问题是面对我们的业务场景该如何去做技术选型，我们先看 Spring 官方经典的微服务架构图

![](https://spring.io/images/diagram-microservices-88e01c7d34c688cb49556435c130d352.svg)

微服务的核心组件由：网关，服务注册发现，服务配置，熔断限流等组成

{% note info %}
注意这里微服务主要以 `Alibaba` 系相关的开源组件为基础构建，并非是 [`Spring Cloud Alibaba`](https://github.com/alibaba/spring-cloud-alibaba) 项目的照搬，而是基于企业实际业务需求的抽象整合，只为提高效率、总结编程套路以及提升编程思想
{% endnote %}

## 选型

- 编程语言：[Oracle JDK 8](https://docs.oracle.com/javase/8/)
- 构建工具：[Gradle](https://gradle.org)
- 网关路由：[Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway)
- 服务通信：[Dubbo](https://dubbo.apache.org/zh)
- 消息管理：[RockerMQ](http://rocketmq.apache.org)
- 分布式事务：[Seata](http://seata.io/zh-cn)
- 注册中心及配置中心：[Nacos](https://nacos.io/zh-cn)
- 限流，熔断，降级：[Sentinel](https://sentinelguard.io/zh-cn)
- 文档管理：[knife4j](https://doc.xiaominfo.com) + [Dubbo-Api-Docs](https://dubbo.apache.org/zh/blog/2020/12/22/dubbo-api-docs-apache-dubbo%E6%96%87%E6%A1%A3%E5%B1%95%E7%A4%BA%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7/)
- 链路追踪：[Zipkin](https://zipkin.io)
- 部署发布：[Docker](https://www.docker.com) + [Nexus Repository OSS](https://www.sonatype.com/nexus/repository-oss)
- 运维监控：[Prometheus](https://prometheus.io) + [Grafana](https://grafana.com)

## 相关问题

### OracleJDK 与 OpenJDK 的区别

有人会说了，这有啥好说的，我们在公司开发都是用 OracleJDK 的。曾经我也以为这两个区别不是很大，看公司的使用情况了，直到我使用了 CentOS 7 系统默认带的 OpenJDK 来编译 Gradle 项目，死活是编译不过，总是提醒我找不到 `tools.jar` 包。有图有真相

![](https://cdn.nlark.com/yuque/0/2021/png/168023/1612353858609-c1d95693-4f19-4c66-b6ad-ed3358abe938.png)

一开始，我把以为是环境配置的问题，但是经过一番折腾，卸载了自带的 OpenJDK，然后再用 `yum install java` 命令去安装 OpenJDK，发现并不是环境的问题，而是系统自带的这个 OpenJDK 中是没有包含 `tools.jar` 文件

### Gradle or Maven

关于如何使用 Gradle 构建项目，以及使用 Gradle 配置符合企业敏捷开发需求，可查看我的 [Gradle](https://incoder.org/tags/Gradle) 系列的文章

## jar 与 bootJar

## SpringBoot 与 SpringCloud

## 单项目 or 聚合项目

## 附录

* [传统行业转型微服务的挖坑与填坑](https://zhuanlan.zhihu.com/p/57385234)
* [技术中台建设为什么比较愿意引入Dubbo而去掉Cloud体系](https://zhuanlan.zhihu.com/p/101529698)
* [Spring Cloud 及 Dubbo 优缺点分析](https://zhuanlan.zhihu.com/p/85012048)
* [Spring Cloud Gateway VS Zuul 比较，怎么选择](https://segmentfault.com/a/1190000019137876)
* [微服务架构：如何用十步解耦你的系统](https://www.javazhiyin.com/56510.html)
* [Dubbo架构中，为什么一般不用Gateway层](https://segmentfault.com/q/1010000013082803)
* [Nacos从入门到避坑](https://blog.csdn.net/qq_33619378/category_9291906.html)
* [利用Spring Boot+Dubbo 构建分布式微服务，全程注解开发](https://mp.weixin.qq.com/s/V8CkJ7hI3QFz9qHRXVP1Tw)
* [SpringBoot 下整合 Dubbo 和 Nacos](https://juejin.im/post/6844903806833000455)
* [SpringCloud项目中接入Nacos作为配置中心](https://www.larscheng.com/config-service/)
* [基于Prometheus搭建SpringCloud全方位立体监控体系](https://www.throwable.club/2018/11/10/spring-cloud-prometheus/)
* [芋道 Spring Boot 监控平台 Prometheus + Grafana 入门](http://www.iocoder.cn/Spring-Boot/Prometheus-and-Grafana/?self)