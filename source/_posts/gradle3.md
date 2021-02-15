---
title: Gradle（三）SpringBoot 单工程
date: 2020-12-16 13:30:46
categories: Gradle
tag: [Gradle]
---

在 [Gradle（一）基础](https://incoder.org/2020/12/10/gradle1/) 的文章中，我们已经对 Gradle 有了一定的认识，本篇来看一看在后端开发中使用 Gradle 构建 SpringBoot 项目的开发。通常有两种方式来构建项目，第一种：每个功能模块即是一个代码工程，用一个 Git 仓库来管理，每个模块只负责完成一件事情；第二种：整个系统的多个模块聚合在一个代码工程里面，也就是我们常说的多模块项目，本篇先来讲一讲单工程

<!-- more -->

## 工程选择

对于单工程，和聚合工程的选择主要根据你所在项目团队的大小，项目分工，以及项目的复杂程度等来考虑。

单工程：适用于项目分工明确，项目庞大复杂，架构服务边界划分明确，配套的自动化等设施完善
聚合工程：适用于项目人员不是很多，项目功能一般，需要一个人集中化管理等

## 环境

* OS：macOS 11.1
* JDK：JDK1.8
* Gradle：6.7.1-bin
* IDE：IntelliJ IDEA Community 2020.3
* SpringBoot：2.4.1

## build.gradle

```groovy
// 项目使用插件，可从 https://plugins.gradle.org 库中寻找合适的插件
plugins {
    id 'org.springframework.boot' version '2.4.1'
    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
    id 'java'
}

// 这里和 maven类似，用于项目唯一坐标
group = 'com.example'
version = '0.0.1-SNAPSHOT'
// 项目兼容版本
sourceCompatibility = '1.8'

// 依赖第三方jar从哪个仓库去下载
repositories {
    mavenCentral()
}

// 项目所需的第三方依赖
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

// 测试相关
test {
    useJUnitPlatform()
}
```

一个 SpringBoot 项目基本的 build.gradle 文件由 plugins，项目坐标，repositories，dependencies，test 基础内容组成。关于 plugins 使用常见有两种方式，核心的依赖，是没有版本号，它和你使用的 Gradle 关联，你无需过多关系这些核心插件的依赖版本

```groovy
// 旧方式
apply plugin: 'java'

// 新方式（推荐）
plubins {
    id 'java'
}
```

## settings.gradle

用于项目模块管理，由于这个单工程，这里只有一个模块

```groovy
rootProject.name = 'demo'
```

## 多环境

可通过自定义 task 来出来

```groovy
// prod
tasks.register("bootRunProd") {
    group = "application"
    description = "Runs the Spring Boot application with the prod profile"
    doFirst {
        tasks.bootRun.configure {
            systemProperty("spring.profiles.active", "prod")
        }
    }
    finalizedBy("bootRun")
}

// dev
tasks.register("bootRunDev") {
    group = "application"
    description = "Runs the Spring Boot application with the dev profile"
    doFirst {
        tasks.bootRun.configure {
            systemProperty("spring.profiles.active", "dev")
        }
    }
    finalizedBy("bootRun")
}
```

启动方式

* 方式一：图形化界面中，直接运行对应环境
  ![](https://res.cloudinary.com/incoder/image/upload/v1609691501/blog/gradle-task-gui.png)
* 方式二：在命令行中，使用命令来运行对应环境，比如 `gradlew bootRunDev`
  ![](https://res.cloudinary.com/incoder/image/upload/v1609691543/blog/gradle-task-terminal.png)

* 方式三：当然你也可以在启动时指定你需要激活的环境
  ```bash
  # 这里激活的 test 环境，把 ${jar_name} 参数换成对应启动的应用文件
  java -jar ${jar_name} --spring.profiles.active=test
  ```

## 排除依赖

```groovy
testImplementation('org.springframework.boot:spring-boot-starter-test') {
    exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
}
```

## 打包

打包时需要，注意我们的 SpringBoot 应用它本质上是一个 bootJar（Fatjar） 应用，因此需要将应用打成一个 bootJar（Fatjar）。而对于什么是 bootJar 和 jar 的区别，可以查看之前在 SpringBoot（二） 启动分析JarLauncher 文章中对于 [jar 规范](https://incoder.org/2019/07/05/springboot2/#jar%E8%A7%84%E8%8C%83) 说明

打包方式

* 方式一：图形化操作

* 方式二：命名执行
  ```bash
  # 在项目的根目录执行，Windows 使用：gradlew；Linux/macOS：./gradlew
  # 当然如果那你已安装且配置好 gradle 的环境，你可以直接使用 gradle 代替 ./gradlew 的相关命令
  gradlew bootJar
  ```

## 发布



## 参考

1. [SpringBoot+gradle 构建多模块项目](https://blog.csdn.net/formularoom/article/details/70354562)
2. [IDEA 2020.2 + Gradle 6.6.1 + Spring Boot 2.3.4 创建多模块项目](https://blog.csdn.net/zh452647457/article/details/108844078)
3. [Spring-boot 2.3.x 源码基于Gradle编译](https://blog.csdn.net/buhuiguowang/article/details/110700585)
4. [用 Gradle 构建 Spring Boot 项目](https://www.cnblogs.com/davenkin/p/gradle-spring-boot.html)
5. [使用 Gradle 构建 springboot 多模块项目,并混合groovy开发](https://www.cnblogs.com/houzheng/p/11024865.html)
6. [Spring Boot Gradle Plugin Reference Guide](https://docs.spring.io/spring-boot/docs/current/gradle-plugin/reference/htmlsingle/)