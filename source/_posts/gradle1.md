---
title: Gradle（一）基础
date: 2020-12-10 10:11:46
categories: Gradle
tag: [Gradle]
---

GitHub 上 Gralde 是这样描述，"Adaptable, fast automation for all"（让一切都能`快速`的`自动化`）
Gradle是一个构建工具，专注于构建自动化和对多语言开发的支持。对于在任何平台上的构建，测试，发布和部署，Gralde 提供了一种灵活的模型，可以支持从编译和打包代码到发布网站的整个生命周期。Gralde 旨在支持跨多种语言和平台的构建自动化，包括 Java，Scala，Android，Kotlin，C/C++ 和 Groovy，并于开发工具和包括 Eclipse，IntelliJ 和 Jenkins 的持续集成服务器紧密集成

<!-- more -->

* Gradle official：https://gradle.org
* Gradle docs：https://docs.gradle.org
* Gradle plugins：https://plugins.gradle.org

## Gradle 特点

1. Gradle 基于 JVM 的构建工具
2. 兼容支持 Maven，Ant 等
3. 支持基于 Groovy 的构建脚本
4. 编译构建执行效率更高
5. 支持多种语言等
6. 易于迁移

## Gradle 安装配置

* Gradle 官方：https://services.gradle.org/distributions/
* Tencent 镜像：https://mirrors.cloud.tencent.com/gradle/

> Tencent Gradle 镜像同步有一定的延迟，需要注意

### 下载

下载需要的版本即可，推荐最新版，这里以最新稳定版 6.7.1 为例，每个正式版本包含如下文件，我们选择 `xxx-bin.zip`（二进制版，只包含了二进制文件（可执行文件），没有文档和源代码） 或 `xxx-all.zip`（完整版，包含了各种二进制文件，源代码文件，和离线的文档）的文件即可，进行手动安装

```sh
gradle-6.7.1
    ├── gradle-6.7.1-wrapper.jar.sha256     # wrapper.jar hash 校验文件 
    ├── gradle-6.7.1-docs.zip               # gradle 文档压缩文件
    ├── gradle-6.7.1-docs.zip.sha256        # gradle 文档 hash 校验文件
    ├── gradle-6.7.1-src.zip                # gradle 源码版，只包含了 Gradle 源代码，不能用来编译你的工程
    ├── gradle-6.7.1-src.zip.sha256         # gradle 源码版 hash 校验文件
    ├── gradle-6.7.1-bin.zip                # gradle 核心压缩文件
    ├── gradle-6.7.1-bin.zip.sha256         # gradle 核心 hash 校验文件
    ├── gradle-6.7.1-all.zip                # gradle 全部资源压缩文件
    └── gradle-6.7.1-all.zip.sha256         # gradle 全部资源 hash 校验文件
```
当然如果你使用的 macOS 系统，且也已经安装了 `homebrew` 包管理工具，那么同样你也可以使用 brew 命令来安装 Gradle，那么你将不需要再去手动配置 Gradle 的环境，它的安装默认路径在 `/usr/local/bin/gradle`，安装完成后你就可以使用 gradle 的相关命令
```bash
# gradle 安装
brew install gradle
# gradle 升级
brew upgrade gradle
# 检查是否安装成功
gradle -v
```

### 配置

手动下载解压的文件进行安装，则需要配置 Gradle 的环境，这样方便我们在任何地方都可以调用 Gradle 的命令，对于 macOS 上手动安装配置 Gradle 环境的操作，可以参考 [MacBook Pro 初始化](https://incoder.org/2018/11/10/mac-init/#Gradle配置) 这篇文章 Gradle 配置

对于 Windows 系统，按照如下步骤进行添加环境变量，我这里 Windows 上为了和项目中 Gradle 版本有所区分，配置的是 6.7 版本
![](https://res.cloudinary.com/incoder/image/upload/v1608634993/blog/gradle-home.png)
![](https://res.cloudinary.com/incoder/image/upload/v1608634993/blog/gradle-path.png)
配置完成后，老规矩我们需要验证下我们的配置是否生效，在命令行中输入 `gradle  -v` 命令，查看有 Gradle 相关的版本信息提示，我们的配置就已成功
![](https://res.cloudinary.com/incoder/image/upload/v1608634993/blog/gradlew-or-gradle.png)

#### GRADLE_HOME

GRADLE_HOME 这个环境变量，它主要是我们手动配置指定 GRADLE 使用的命令环境

#### GRADLE_USER_HOME

GRADLE_USER_HOME 指配置 Gradle 的安装下载的路径。默认 `/Users/<PC NAME>/.gradle` 路径，如果你在系统环境中设置了 GRADLE_USER_HOME 的环境变量，那么下载的路径就变成了你自定义设置的路径

## Gradle 基础

刚刚在上面我们配置时，使用了 `gradlew` 命令，那这个又是啥呢，这里简单解释下，gradlew 是 gradle wrapper 的简写，对于 Gradle 构建的项目，用于解决 Gradle 安装，部署以及统一项目的 Gradle 的构建版本等一系列问题。 

Gradle 有两个基本的概念：project 和 task，Gradle 里面的所有东西基于这两个概念
* project：通常指一个项目
* task：指构建过程中的任务

一次构建可以有 1 到 n 个 project，而每个 project 有 1 到 n 个 task

## Gradle 项目

Android 项目工程一开始就默认使用 Gradle 来构建，在 Android 领域里使用花样也是比较多，更好体现了 Gradle 的灵活性，对于后端 Spring 系列项目，现在也是越来越多的开始使用 Gradle 来构建了，在 [Spring Boot 2.3.0.M1](https://spring.io/blog/2020/06/08/migrating-spring-boot-s-build-to-gradle) 版本官方已开始在生产环境开始使用 Gradle 代替 Maven 进行构建，测试，发布项目。这从侧面也印证了 Gradle 对于复杂庞大的系统更加友好和**高效**。

对于使用 Gradle 构建的 Android 项目也好，Java 项目也好，还是 SpringBoot 项目也罢，它们都有共同的特点。在结构上有下面的相同点

```bash
project
    ├── ……
    ├── .gradle/                            # 项目使用 gradle 编译生成的临时文件存放位置
    ├── gradle/wrapper
    │    │── gradle-wrapper.jar             # gradlew 核心执行文件
    │    └── gradle-wrapper.properties      # gradle 运行环境配置文件
    ├── build.gradle                        # 项目依赖配置，脚本配置文件
    ├── gradlew                             # Linux or macOS 下可执行脚本
    ├── gradlew.bat                         # Windows 下可执行脚本
    ├── settings.gradle                     # 配置构建应用时应将哪些模块包含在内
    └── ……
```

### gradle-wrapper.properties

* gradle-wrapper.jar 文件是项目中执行 gradlew 相关命令的具体实现，感兴趣的可以查看其中的具体源码实现
* gradle-wrapper.properties 是 Gradle 项目版本管理的核心
  * distributionBase=GRADLE_USER_HOME：指定了 wrapper 保存下载的 Gradle 的`主路径`
  * distributionPath=wrapper/dists：指定了 wrapper 保存下载的 Gradle 的`子路径`
  * zipStoreBase=GRADLE_USER_HOME：指定了 wrapper 保存下载 gradle-6.7.1-bin.zip 文件的`主路径`
  * zipStorePath=wrapper/dists：指定了 gradle-6.7.1-bin.zip 文件的`子路径`
  * distributionUrl=https\://services.gradle.org/distributions/gradle-6.7.1-bin.zip：gradle 文件下载的源地址

{% note info %}
distributionBase 和 zipStoreBase 有两种取值
* GRADLE_USER_HOME：默认使用方式，表示用户目录，默认路径 `/Users/<PC NAME>/.gradle`
* PROJECT：表示工程的当前目录，不常用
{% endnote %}

对应 Gradle 的下载及解压目录这里还需要注意下
{% note danger %}
Gradle 的存放地址，比如：~/.gradle/wrapper/dists/gradle-6.7.1-bin/`bwlcbys1h7rz3272sye1xwiv6` 这里一个看起来无规则的文件夹，我们的 gradle 下载及解压必须放在这个文件夹内，而这个看似无规则的文件夹，实质是根据 distributionUrl 路径字符串计算 md5 值得来的
{% endnote %}

### build.gradle 及 settings.gradle

对于`build.gradle` 及 `settings.gradle` 文件在 Android 应用和 SpringBoot 应用是不一样，因此关于他两介绍请移步 [Gradle（二）Android](https://incoder.org/2020/12/15/gradle2)，[Gradle（三）SpringBoot]() 文章进行查看

## Gradle 依赖

用于声明依赖关系的配置

| 配置名称               | 角色          | 是否可消费 | 是否可分解 | 描述                                                        |
|:------------------:|:-----------:|:-----:|:-----:|:---------------------------------------------------------:|
| `api`              | 声明API依赖项    | N     | N     | 在这里，您可以声明依赖关系，这些依赖关系会在编译时和运行时以可传递方式导出到使用者                 |
| `implementation`   | 声明实现依赖性     | N     | N     | 在这里，您可以声明纯属内部的依赖关系，而不是要向使用方公开（在运行时仍向使用方公开）                |
| `compileOnly`      | 声明仅编译依赖项    | N     | N     | 在这里可以声明在编译时需要的依赖项，而在运行时则不需要。这通常包括在运行时找到时会被阴影化的依赖项         |
| compileOnlyApi     | 声明仅编译API依赖项 | N     | N     | 在这里，您可以声明模块和使用者在编译时需要的依赖项，而在运行时则不需要。这通常包括在运行时找到时会被阴影化的依赖项 |
| `runtimeOnly`      | 声明运行时依赖项    | N     | N     | 在这里可以声明仅在运行时才需要的依赖关系，而在编译时则不需要                            |
| testImplementation | 测试依赖        | N     | N     | 在这里声明用于编译测试的依赖项                                           |
| testCompileOnly    | 声明测试仅编译依赖项  | N     | N     | 在这里声明仅在测试编译时需要的依赖项，而不应泄漏到运行时。这通常包括在运行时找到时会被阴影化的依赖项        |
| testRuntimeOnly    | 声明测试运行时依赖项  | N     | N     | 在这里可以声明仅在测试运行时才需要的依赖项，而在测试编译时则不需要                         |

核心需要掌握的是 `api`，`implementation`，`compileOnly`，`runtimeOnly` 这4种依赖方式

>对于你可能看到依赖方式，compile（api），provided（compileOnly），apk（runtimeOnly） 这些方式是比较旧的依赖方式，在 gradle plugin 3.0 开始已废弃，请使用新的依赖方式

### 本地依赖

#### 本地依赖 module lib 

通过这种方式依赖的弊端是每次都需要构建 module，但 module 比较多时构建非常耗时，建议控制 module 的依赖数量，避免构建耗时

```groovy
// module 需要在项目根目录下的 settings.gradle 中通过 include 引入
implementation project(':libname')  
```

#### 本地二进制 lib 依赖

本地的 jar 或者 aar 需要放在 module 的 libs 文件夹下，通过这种方式依赖

##### 依赖 jar

```groovy
// 方式一：可以一次性依赖 libs 下的所有 jar
implementation fileTree(dir: 'libs', include: ['*.jar'])

// 方式二：可以指定依赖一个或几个 jar
implementation files('libs/xxxx1.jar', 'libs/xxxx2.jar')
```

##### 依赖 aar

```groovy
// 在 module 的 build.gradle 中添加目录指定
repositories {
    flatDir {
        dirs 'libs'
    }
}

// 在 dependencies 中加入对 aar 的引入
// 方式一：可以一次性依赖 libs 下所有的 aar
implementation fileTree(dir: 'libs', include: ['*.aar'])
// 方式二：可以指定依赖某一个aar
implementation files(name: 'aar-lib-name', ext: 'aar')
```

### 远程二进制 lib 依赖

```groovy
// 依赖明确的版本，标明 group、name 和 version
implementation group: 'org.mybatis.spring.boot', name: 'mybatis-spring-boot-starter', version: '2.1.1'

// 常用的简写方式引用
implementation 'org.mybatis.spring.boot:mybatis-spring-boot-starter:2.1.1'
```

## Gradle 命令

在项目中推荐使用 gradlew 命令来进行执行，这样本质是使用项目所依赖的 Gradle 版本进行执行。当然如果你本地配置了 Gradle 的环境变量，你可以将 `gradlew` 命令更改成 `gradle` 来执行

* gradlew clean: 清除 build 文件夹
* gradlew check: 执行 lint 检查
* gradlew assemble: 编译并打包你的代码，但并不运行单元测试
* gradlew build: 编译和测试你的代码，并生成一个包含所有类与资源的文件
* gradlew dependencies: 查看所有依赖库
  * gradlew dependencies -configuration runtime: 查看运行时依赖库

{% note warning %}
注意：
* Windows：在项目根目录，使用的是 `gradlew`
* Linux or macOS：在项目的根目录，使用的是 `./gradlew`
{% endnote %}

## 总结

1. 对于 Gradle 我们不需要配置 GRADLE_USER_HOME 的环境，原因是项目中已对使用 Gradle 的版本做出了统一，我们仅需要根据自身的网络需要（如果从默认地址下载很慢，则需要配置好项目依赖镜像源）做出合适的配置。而如果你需要在任何地方使用 `gradle` 相关的命令，则配置 GRADLE_HOME 即可
2. 依赖方式，我们选择 implementation 方式，这样可屏蔽掉不同应用之间因为引用了同一 lib 而不同版本造成的麻烦问题等

## 参考

* [gradle-wrapper.properties中各属性的含义](https://blog.csdn.net/u013553529/article/details/55011602)
* [Dependency management in Gradle](https://docs.gradle.org/current/userguide/core_dependency_management.html)
* [The Java Library Plugin](https://docs.gradle.org/current/userguide/java_library_plugin.html#sec:java_library_configurations_graph)
* [The Distribution Plugin](https://docs.gradle.org/current/userguide/distribution_plugin.html)