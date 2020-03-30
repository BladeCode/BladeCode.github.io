---
title: SpringBoot（五）多环境配置
date: 2020-02-02 15:43:00
categories: SpringBoot
tag: [SpringBoot]
---

在 SpringBoot 项目中，常用的包管理分别为 maven 和 gradle，在不同包管理下我们如何实现多环境的项目配置，这是实际项目开发过程汇总必备的一项技能，可以大大提高我们开发部署效率，同时也避免了人为的频繁改动配置造成的问题等，有些人可能会问了，maven 不是用的好好的嘛，干嘛还要用 gradle，首先我们可以看现在主流开源项目在提供引入方式时都是有提供了 gradle 依赖方式，以及 gradle 支持编写脚本，在很大程度上让管理更加便捷和人性化

<!-- more -->

废话不多说，我们直接来看代码吧，首先我们先来看看使用 maven 来进行多环境的配置

## maven

### pom.xml 文件

```xml
<!-- pom.xml <project>标签下配置环境变量名 -->
<profiles>
    <profile>
        <!-- 本地开发环境 -->
        <id>dev</id>
        <properties>
            <profileActive>dev</profileActive>
        </properties>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <profile>
        <!-- 正式环境 -->
        <id>prod</id>
        <properties>
            <profileActive>prod</profileActive>
        </properties>
        <activation>
            <activeByDefault>false</activeByDefault>
        </activation>
    </profile>
</profiles>

<!-- pom.xml <project>标签下配置打包根据环境导入的文件 -->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
    <!-- 文件加载配置-->
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
            <excludes>
                <exclude>application*.yml</exclude>
            </excludes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
            <includes>
                <include>application.yml</include>
                <include>application-${profileActive}.yml</include>
            </includes>
        </resource>
    </resources>
</build>
```

### application*.yml 文件

根据上面我们的配置，我们首先排除 <font color=red>resources</font> 目录下的所有 `application*.yml` 文件，然后再导入 `application.yml`，`application-${profileActive}.yml` 文件，那么通常在 `application.yml` 文件中我们放置一些与环境无关的配置，在 `application-${profileActive}.yml` 文件中根据不同环境配置不同的属性（比如：数据库连接，日志等级等配置）

根据 SpringBoot 加载资源文件的顺序，如果我们在 `application-${profileActive}.yml` 文件中配置了与 `application*.yml` 文件相同的属性，那么 `application-${profileActive}.yml` 会覆盖掉 `application*.yml` 文件中相同属性配置

#### application.yml

```yml
# 动态激活运行的环境，默认是 dev
#     当然你也可以在你的 pom.xml 文件中进行默认激活的环境更改
spring:
  profiles:
    active: @profileActive@
```

## gradle


### 编译打包

## 部署

关于应用的部署，可以参考[IDEA 之 SpringBoot 应用部署](https://incoder.org/2019/11/19/deploy-springboot/)，这里不再过多进行说明

## 附录

* [SpringBoot 2.1.6.RELEASE 官方指南](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/)
* [SpringBoot 中文指南](https://www.springcloud.cc/spring-boot.html)
* [Gradle Builds From Apache Maven](https://docs.gradle.org/current/userguide/migrating_from_maven.html#migmvn:profiles_and_properties)
* [灵活强大的构建系统 Gradle](https://tech.meituan.com/2014/08/18/gradle-practice.html)