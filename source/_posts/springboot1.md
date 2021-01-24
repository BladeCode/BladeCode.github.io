---
title: SpringBoot（一） 初识
date: 2019-06-23 09:10:11
categories: SpringBoot
tag: [SpringBoot]
---

![](https://res.cloudinary.com/incoder/image/upload/v1561900597/blog/springboot.jpg)

<!-- more -->

从本篇文章开始，记录学习 SpringBoot 框架在实践，源码方面的知识，本节是第一篇，因此不涉及相关复杂知识的学习。众所周知，随着微服务的广泛流行，Spring 系列的 SpringBoot 和 SpringCloud 的应用也更受欢迎，那么请跟随我的脚本来一步步解开 SpringBoot 她神秘的面纱

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


## Spring 运行

### 命令

#### macOS or Linux

```bash
# 项目路径下(spring-start)
gradle bootRun
```

#### Windows

```bash
# 项目路径下(spring-start)
./gradle bootRun
```

### 运行说明

![spring-running-logo](https://res.cloudinary.com/incoder/image/upload/v1562167001/blog/spring-running-logo.png)


## Spring 打包

### jar 分析

![springboot-deploy-jar-unzip](https://res.cloudinary.com/incoder/image/upload/v1561259381/blog/springboot-deploy-jar-unzip.png)

目录说明
```
project/
├── BOOT-INF/                                                                   
│   ├── classes                                                                 # 当前项目结果文件放置在 classes 路径下
│   │   │   └── application.properties                                          # 项目中配置文件
│   │   ├── org/                                                                # 项目中 java 路径下，编译成 class 文件路径
│   │   ├── static/                                                             # 项目中 resources 路径下的静态文件夹
│   │   └── templates/                                                          # 项目中 resources 路径下的模板文件夹
│   └── lib/                                                                    # 项目所依赖的第三方 jar（Tomcat，SpringBoot 等）
├── META-INF/                                                                   
│   └── MANIFEST.MF                                                             # 清单文件，用于描述可执行 jar 的一些基本信息
└── org/springframework/boot/loader/                                            # jar 包启动相关的引导
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

#### MANIFEST.MF

```jar
Manifest-Version: 1.0                                               # 清单版本号
Start-Class: org.incoder.start.SpringbootStartApplication           # 项目 main 方法所在的类
Spring-Boot-Classes: BOOT-INF/classes/                              # 项目相关代码在打包后 jar 中的路径
Spring-Boot-Lib: BOOT-INF/lib/                                      # 项目中所依赖的第三方 jar 在打包后 jar 中的路径
Spring-Boot-Version: 2.1.6.RELEASE                                  # 项目  SpringBoot 版本
Main-Class: org.springframework.boot.loader.JarLauncher             # 当前 jar 文件的执行入口类（main 方法所在的类）
回车换行（在清单文件中，必须有，否则会出错）
```

#### org/springframework/……目录
项目中引入的第三方 jar 中并不包含`org/springframework/boot/loader`内容，那这个目录是从哪里来的呢？

寻找最终发现是项目中我们的`build.gradle`文件中，引入的`org.springframework.boot:spring-boot-gradle-plugin`依赖，而这个依赖位于`classpath`下，说明引入的这个插件 **仅仅** 是在项目构建时才起作用，当项目进行打包后，并不会把插件包打入到项目的依赖库中，也就是`BOOT-INF/lib/`路径下

如何去研究在`org/springframework/boot/loader`下的源码内容呢？
最好的方式是在项目的依赖中导入`org.springframework.boot:spring-boot-loader`依赖

>原则上，在项目开发过程中是不需要引入`org.springframework.boot:spring-boot-loader`依赖，这里只是为了方便阅读源码进行学习

## Spring 其他

### 配置文件格式

* [properties](https://en.wikipedia.org/wiki/.properties)
* **推荐** [yml](https://en.wikipedia.org/wiki/YAML)

### 常用命令

#### gradle tasks

表示获取当前工程可用的 gradle tasks命令

##### Application tasks

* bootRun：Runs this project as a Spring Boot application.（以 SpringBoot 的形式运行当前项目）

##### Build tasks

* bootJar：Assembles an executable jar archive containing the main classes and their dependencies.（装配一个可执行的 jar（自包含的 jar 包，不依赖其他容器） 归档，这个归档 jar 中包含了所需的依赖以及主类等）

##### Run jar

```bash
java -jar jar-name.jar
```

##### Other
```
# 解压 jar 到当前 start 目录下
unzip start-0.0.1-SNAPSHOT.jar -d ./start
```