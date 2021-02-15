---
title: 微服务架构 - Alibaba 生态整合（一）
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
- 文档管理：[SpringFox](http://springfox.github.io/springfox) + [Knife4j](https://doc.xiaominfo.com) + [Dubbo-Api-Docs](https://dubbo.apache.org/zh/blog/2020/12/22/dubbo-api-docs-apache-dubbo%E6%96%87%E6%A1%A3%E5%B1%95%E7%A4%BA%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7/)
- 部署发布：[Docker](https://www.docker.com) + [Nexus Repository OSS](https://www.sonatype.com/nexus/repository-oss)
- 运维监控：[Prometheus](https://prometheus.io) + [Grafana](https://grafana.com)

![](https://res.cloudinary.com/incoder/image/upload/v1616294894/blog/Spring-Cloud-Alibaba.png)

## SpringCloud VS SpringCloud Alibaba

这里我汇总到表格中，方便查看比较

![](https://res.cloudinary.com/incoder/image/upload/v1616302368/blog/Spring_Cloud_VS_Spring_Cloud_Alibaba.png)

## 相关问题

### JDK

#### OpenJDK

Java 最早由 SUN（Sun Microsystems，发起于美国斯坦福大学，SUN是Stanford University Network的缩写）发明，2006 年 SUN 公司将 Java 开源，此时的 JDK 即为 OpenJDK

[OpenJDK](http://openjdk.java.net/) 是 Java SE 的开源实现，由 SUN 和 Java 社区提供支持，2009 年 Oracle 收购了 SUN 公司，自此 Java 的维护方之一的 SUN 也就变成了 Oracle

大多数 JDK 都是在 OpenJDK 的基础上编写实现的，比如 IBM J9，Azul Zulu，Azul Zing 和 Oracle JDK。几乎所有的 JDK 都派生自 OpenJDK，他们之间不同的是授权许可证。常见的 OpenJDK 发行商

| **发行商**                  | **长期支持（TLS）** | **许可证（license）** | **TCK 测试** | **未修改的上游构建** | **提供商业支持**       |
|:------------------------:|:-------------:|:----------------:|:--------------:|:------------:|:----------------:|
| [AdoptOpenJDK](https://adoptopenjdk.net/)             | Yes           | Yes              | <font color=red>No</font> | Optional     | Optional（IBM）    |
| [Alibaba Dragonwell](http://dragonwell-jdk.io/)       | Yes           | Yes              | Yes            | <font color=red>No</font> | <font color=red>No</font> |
| [Amazon Corretto](https://aws.amazon.com/corretto/)          | Yes           | Yes              | Yes            | <font color=red>No</font> | Optional</br>（on AWS） |
| [Azul Zulu](https://www.azul.com/downloads/zulu-community/?package=jdk)                | Yes           | Yes              | Yes            | <font color=red>No</font> | Optional         |
| [BellSoft Liberica JDK](https://bell-sw.com/pages/downloads/)    | Yes           | Yes              | Yes            | <font color=red>No</font> | Optional         |
| [IBM Java JDK](https://www.ibm.com/support/pages/java-sdk-downloads)             | Yes           | <font color=red>No</font> | Yes            | <font color=red>No</font> | Yes              |
| [ojdkbuild](https://github.com/ojdkbuild/ojdkbuild)                | Yes           | Yes              | <font color=red>No</font> | Yes          | <font color=red>No</font> |
| [OpenLogic OpenJDK](https://www.openlogic.com/openjdk-downloads)        | Yes           | Yes              | <font color=red>No</font> | <font color=red>No</font> | Optional         |
| [Oracle Java SE](https://www.oracle.com/java/technologies/javase-downloads.html)           | Yes     | <font color=red>No</font> | Yes            | <font color=red>No</font> | Yes              |
| [Oracle OpenJDK](https://openjdk.java.net/)           | <font color=red>No</font> | Yes              | Yes            | Yes          | <font color=red>No</font> |
| [Red Hat OpenJDK](https://developers.redhat.com/products/openjdk/download) | Yes           | Yes              | Yes            | <font color=red>No</font> | Yes              |
| [SAP SAPMachine](https://sap.github.io/SapMachine/)           | Yes           | Yes              | Yes            | <font color=red>No</font> | <font color=red>No</font> |

>TLS：long-term support，长期支持（LTS）是一种产品生命周期管理策略，在该策略中，与标准版相比，计算机软件的稳定版本可以维持更长的时间。该术语通常保留给开源软件，它描述的软件版本比该软件的标准版本支持数月或数年的支持。
>TCK：Technology Compatibility Kit，技术兼容性套件（TCK）是一套测试套件，至少名义上检查Java规范请求（JSR）的特定声称实施是否符合要求

#### OralceJDK

显而易见 OracleJDK 是在 Oracle 收购 SUN 公司之后，基于 OpenJDK 源码构建的 JDK 被命名了 OracleJDK，两则之间没有重大的技术差异

#### 两者的区别


#### 问题

有人会说了，这有啥好说的，我们在公司开发都是用 OracleJDK 的。曾经我也以为这两个区别不是很大，看公司的使用情况了，直到我使用了 CentOS 7 系统默认带的 OpenJDK 来编译 Gradle 项目，死活是编译不过，总是提醒我找不到 `tools.jar` 包。有图有真相

![](https://res.cloudinary.com/incoder/image/upload/v1616514430/blog/gradle-openjre.png)

一开始，我把以为是环境配置的问题，但是经过一番折腾，卸载了自带的 OpenJDK，然后再用 `yum install java` 命令去安装 OpenJDK，发现并不是环境的问题，而是系统自带的这个 OpenJDK 是 JRE，所以并没有包含 `tools.jar` 文件。所以这个问题就是你系统 JDK 的问题了。建议卸载 JRE，重新安装 JDK

```bash
# 可以先查找 JDK，下面命令是我查找 java-1.8 的相关应用
yum search java-1.8 | grep -i --color JDK
# 也可以直接安装 JDK，比如我这里提供的 java-1.8.0-openjdk-devel.x86_64
yum install java-1.8.0-openjdk-devel.x86_64
```

![](https://res.cloudinary.com/incoder/image/upload/v1616515361/blog/centos-openjdk.png)

### Gradle or Maven

关于如何使用 Gradle 构建项目，以及使用 Gradle 配置符合企业敏捷开发需求，可查看我的 [Gradle](https://incoder.org/tags/Gradle) 系列的文章

### jar 与 bootJar

之前在[《SpringBoot（二） 启动分析JarLauncher》](https://incoder.org/2019/07/05/springboot2/)文章中进行对 SpringBoot 应用启动做了分析，提到了 [jar 规范](https://incoder.org/2019/07/05/springboot2/#jar%E8%A7%84%E8%8C%83)，做了简单的介绍，那么本篇在此基础上进一步的完善这个知识点

> 这里以 [rc-microservices-alibaba](https://github.com/RootCluster/rc-microservices-alibaba) 项目的 `microservices-alibaba-gateway` 模块的编译为例

#### jar

jar（Java Archive）可以看做是特殊文件压缩的一种，通常用于聚合大量的 Java 类文件，相关的元数据和资源文件到一个文件，以便分发 Java 平台应用软件或库。jar 文件是一种归档文件，以 ZIP 格式构建，以 `.jar` 为文件扩展名。包含一个可选的 `META-INF` 目录，可以通过命令行 jar 工具或使用 Java 平台中的 `java.util.jar` API 创建 jar 文件

可以看到，我们打包成 jar 的文件，仅仅是源码+资源文件，以及生成的 `META_INF` 文件

```text
microservices-alibaba-gateway-1.0-SNAPSHOT
    ├── META-INF
    ├── org
    │   └── incoder
    │       └── gateway
    │           ├── config
    │           ├── exception
    │           └── filter
    ├── static
    └── templates
```

#### bootJar

看名字就知道，这是 SpringBoot 的专属 jar。为什么会有这种 jar，原因是在 SpringBoot 出现之前，我们的 jar 应用想要运行，需要将应用放入到 Tomcat 中。而 SpringBoot 的出现改变了这层关系，是 SpringBoot 在打包成 bootJar 时，会内置 Tomcat，我们可以直接运行启动 jar 应用，可能有人会说，这怎么改变了，不都还是运行在 Tomcat 上么。没错它确实依然运行在 Tomcat 上，但是他们的加载方式改变了

我们可以看到，打成 bootJar 的文件，除了 `META-INF` 相关文件，并且包含了 `BOOT-INF` 的 lib 路径下存放项目所使用的所有第三方的 jar 包 ，同时在打包的根目录，生成了 SpringBoot 的 loader 相关的文件

```text
microservices-alibaba-gateway-1.0-SNAPSHOT
    ├── BOOT-INF
    │   ├── classes
    │   │   ├── META-INF
    │   │   ├── org
    │   │   │   └── incoder
    │   │   │       └── gateway
    │   │   │           ├── config
    │   │   │           ├── exception
    │   │   │           └── filter
    │   │   ├── static
    │   │   └── templates
    │   └── lib
    ├── META-INF
    └── org
        └── springframework
            └── boot
                └── loader
                    ├── archive
                    ├── data
                    ├── jar
                    ├── jarmode
                    └── util
```

### Spring 生态

1. Spring：一个一站式轻量级Java 开发框架，核心是控制反转（IOC）和面向切面（AOP），针对开发 Web 层，业务层，持久层等提供了多种配置解决方案，也是整个微服务开发的基石
2. SpringMVC：是 Spring 基础之上的一个 MVC 框架，主要处理 Web 开发的路径映射和视图渲染，属于 Spring 框架中 Web 层开发的一部分（开发配置非常繁琐，复杂）
3. SpringBoot：专注于服务方面的接口开发，和前端解耦，默认优于配置，一定程度上取消了 XML 配置，是一套快速开发的脚手架，能快速开发单个微服务
4. SpringCloud：大部分功能组件基于 SpringBoot 去实现，提供了完整的微服务架构的技术生态，SpringCloud 专注于微服务的整合和管理

### 单工程 or 聚合工程

>个人推荐单工程的方式，毕竟聚合工程最终会随着业务的发展推进，需要拆分为单项目开发管理，那还不如一开始就拆分

#### 单工程

这里的单工程是指，每一个模块都是一个项目，由一个仓库进行管理，特点及要求如下

1. 适合团队小组分工明确，开发人员多
2. 适合项目迭代快
3. 需要比较健全的基础设施，比如网关，公共基础工具包，消息管理，以及自动化部署相关服务设施

>相关的构架过程可参考 [Gradle（三）SpringBoot 单工程](https://incoder.org/2020/12/16/gradle3/) 文章

#### 聚合工程

这里的聚合工程是指，将整个系统开发的所有模块以及公共模块都放在一个项目工程中，也就是用同一个仓库来进行管理，特点如下

1. 适合项目初期，项目分工不是特别明确，开发人员少
2. 项目需要集中管理

>相关的构架过程可参考 [Gradle（四）SpringBoot 聚合工程](https://incoder.org/2021/03/06/gradle4/) 文章

### 业务拆分

对于服务的拆分是没有统一的标准，除了通过实际的业务场景，团队能力，人员组织架构等多种因素综合考虑。都根据实际的需求进行调整，对于拆分主要从以下原则去思考

1. 单一职责原则：保证每个服务只做好一件事，体现“高内聚，低耦合”，尽量减少对外界环境的依赖
2. 服务依赖原则：避免服务间的循环依赖，在设计时就需要对服务进行分级，区分核心服务与非核心服务
3. Two Pizza Team原则：让团队保持在2 个披萨就能让队员吃饱的小规模概念

## 参考

* [传统行业转型微服务的挖坑与填坑](https://zhuanlan.zhihu.com/p/57385234)
* [Java官方（Oracle/Sun）发布的JDK，和开源项目OpenJDK，里面包含的JVM是否相同](https://www.zhihu.com/question/19882320)
* [OpenJDK和Oracle JDK有什么区别和联系？](https://cloud.tencent.com/developer/article/1598291)