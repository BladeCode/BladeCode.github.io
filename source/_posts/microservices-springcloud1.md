---
title: 微服务架构 - SpringCloud 生态整合（一）
date: 2020-11-08 07:10:00
categories: Microservices
tag: [Microservices, SpringCloud]
---

微服务这一概念在 2014 年的 3 月份随着 James Lewis 和 Martin Fowler 在博客中对于微服务这一概念做出了详细的阐述，开始走进开发者的视野。在 Spring 官方的加持下，助推微服务架构风格的应用开始火边整个后端领域。在早起微服务化的演进中 Netflix 的一些列开源的组件，迅速占领微服务生态中的 C 位，提供了网关路由，负载均衡，服务注册发现，服务通信，服务熔断限流等核心组件

<!-- more -->

本系列文章是基于 Spring 官方提供的组件，完成相关功能组件的整合，以满足企业需求为目的的学习总结。本篇文章先从 Spring 官网经典的微服务架构图开始

![](https://spring.io/images/diagram-microservices-88e01c7d34c688cb49556435c130d352.svg)

{% note info %}
注意这里主要以 `Spring` 系官方相关的开源组件为基础构建，并非是 [`Spring Cloud`](https://spring.io/projects/spring-cloud) 项目的照搬，而是基于企业实际业务需求的抽象整合，只为提高效率、总结编程套路以及提升编程思想
{% endnote %}

当然这只是我在实际生产实践中的总结，并不一定适合你的业务场景，你也可以参考我的另一个系列 [Alibaba 生态整合](https://incoder.org/2020/11/11/microservices-alibaba1/)，希望能对你有所帮助

## 选型

- 编程语言：[Oracle JDK 8](https://docs.oracle.com/javase/8/)
- 构建工具：[Gradle](https://gradle.org)
- 网关路由：[Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway)
- 服务通信：[OpenFeign](https://github.com/OpenFeign/feign)
- 注册中心：[Eureka](https://github.com/Netflix/eureka)
- 配置中心：[Spring Cloud Config](https://github.com/spring-cloud/spring-cloud-config)
- 限流，熔断，降级：[Hystrix](https://github.com/Netflix/Hystrix)
- 文档管理：[SpringFox](http://springfox.github.io/springfox) + [Knife4j](https://doc.xiaominfo.com)
- 部署发布：[Docker](https://www.docker.com) + [Nexus Repository OSS](https://www.sonatype.com/nexus/repository-oss)
- 链路追踪：[Spring Cloud Sleuth](https://cloud.spring.io/spring-cloud-sleuth) + [Zipkin](https://zipkin.io)

