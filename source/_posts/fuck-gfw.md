---
title: 专治各种网络不服 
date: 2020-02-27 15:00:10
categories: DevTool
tag: [Exp]
---

众所周知国内的开发已经由原来的“致敬”到现在软件生态领域的“引领”（目前来说还未到真正引领）世界技术发展，但是一个问题始终还是未能有所突破，作为中国的开发者每次在面对新的技术在环境搭建就劝退了一众人，很多开发所依赖的项目资源都来自国外服务，而由于中国的特殊，对很多国外服务的限制，让你在开始的第一阶段总是碰的鼻青脸肿，把大量的时间浪费在环境搭建的等待上，虽然现在很多开源组织或者一线大厂提供了相应的镜像服务方便国内的开发者，但很多都还需要我们自行去更改或者解决这些问题，本篇文章就是我的开发之路上的各种网络问题的解决办法

<!-- more -->

## Gradle

Gradle 作为新一代的包管理工具，早期作为 Android 项目的御用包管理，渐渐的越来越多的服务端项目也开始在使用 Gradle 来进行管理，至于 Gradle 和 Maven 的对比，我这里不做评论，请移步 Gradle 官方网站对两个包管理的比较 [Gradle vs Maven Comparison](https://gradle.org/maven-vs-gradle/)

> Gradle 镜像地址：https://services.gradle.org/distributions
> Gradle 腾讯镜像：https://mirrors.cloud.tencent.com/gradle

### 单项目配置

#### Android 项目

在 Android 项目中主要有下面这些配置文件

* build.gradle
  * 项目级别：在项目根目录，定义项目中所有模块共用的 Gradle 代码库和依赖项
  * 模块级别：在模块根目录，用于为其所在的特定模块配置构建设置，可以通过配置这些构建设置提供自定义打包选项（如额外的构建类型和产品变种），以及替换 main/ 应用清单或顶层 build.gradle 文件中的设置
* settings.gradle：项目的根目录下，用于指示 Gradle 在构建应用时应将哪些模块包含在内
* xxxxx.gradle：模块配置文件，可将冗长的配置信息分块进行配置，比如依赖的版本统一管理等

>更详细的介绍请查看 Android 官方说明 [配置构建](https://developer.android.google.cn/studio/build)

项目的依赖仓库镜像配置，在项目级别的 build.gradle 文件中，如下进行镜像的指定示例

```groovy
buildscript {
    repositories {
        // 添加或修改这里指向的镜像仓库地址，默认使用，https://maven.google.com/
        google()
        // 如果需要指定 google 的镜像地址，可注释掉上面的 google()默认配置，使用下面的显示指定配置
        // google{
        //     url 'https://maven.aliyun.com/repository/google'
        // }
        jcenter()
        maven { 
            // 比如修改这里的镜像指向地址
            // url 'https://jitpack.io' 
            url 'https://maven.aliyun.com/repository/public'
        }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.3'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        // 添加或修改这里指向的镜像仓库地址，默认使用，https://maven.google.com/
        google()
        jcenter()
        maven { 
            // 比如修改这里的镜像指向地址
            // url 'https://jitpack.io' 
            url 'https://maven.aliyun.com/repository/public'
        }
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

>我们所依赖的 jar 文件，阿里云镜像中不一定都有，所以我们需要根据具体的项目实际情况调整

#### SpringBoot 项目

在 SpringBoot 项目中主要有下面这些配置文件

* build.gradle：主要配置文件，关于项目的依赖关系主要在该文件中配置
* settings.gradle：项目信息文件，项目的一些可在这里配置，比如项目名称、子项目信息
* xxxxx.gradle：模块配置文件，可将冗长的配置信息分块进行配置，比如依赖的版本统一管理等

如下，build.gradle 配置信息

```groovy
plugins {
    id 'org.springframework.boot' version '2.1.6.RELEASE'
    id 'io.spring.dependency-management' version '1.0.8.RELEASE'
    id 'java'
}

group = 'org.incoder'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8
sourceCompatibility = 1.8

repositories {
    // 显式指定仓库访问地址，以下两个地址推荐阿里云镜像，按顺序执行
   maven {
       // 指向 阿里云 镜像仓库
       url 'https://maven.aliyun.com/repository/public'
   }
//    maven {
//        // 指向 spring 官方仓库
//        url 'http://repo.spring.io/release'
//    }
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springframework.boot:spring-boot-starter-aop'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }
}

test {
    useJUnitPlatform()
}
```

对于 SpringBoot 项目，不管是全局镜像配置还是单项目的镜像配置，可能存在 SpringBoot 依赖的插件依赖无法下载，通常表现为卡在 `Gradle: Download org.springframework.boot.gradle.plugin-xxxx.pom` 这里，那则需要也配置插件镜像，在 settings.gradle 文件<font color=red>**最上面**</font>加入如下配置

```groovy
pluginManagement {
    repositories {
        maven { url "https://maven.aliyun.com/repository/gradle-plugin" }
    }
}
```

### 全局配置

在当前系统 `${USER_HOME}/.gradle/` 目录下创建 `init.gradle` 文件，将 Maven 和 Jcenter 仓库都指向阿里云镜像仓库

```groovy
`// 如果你的 springboot plugin 下载也很慢，也可以全局设置插件下载地址`
pluginManagement {
    repositories {
        maven { url "https://maven.aliyun.com/repository/gradle-plugin" }
    }
}
// 项目依赖第三方包下载地址替换
allprojects{
    repositories {

        def ALIYUN_CENTRAL_URL = 'https://maven.aliyun.com/repository/central/'
        def ALIYUN_JCENTER_PUBLIC_URL = 'https://maven.aliyun.com/repository/public/'
        def ALIYUN_GOOGLE_URL = 'https://maven.aliyun.com/repository/google/'
        def ALIYUN_GRADLE_PLUGIN_URL = 'https://maven.aliyun.com/repository/gradle-plugin/'
        def ALIYUN_SPRING_URL = 'https://maven.aliyun.com/repository/spring/'
        def ALIYUN_SPRING_PLUGIN_URL = 'https://maven.aliyun.com/repository/spring-plugin/'
        def ALIYUN_GRAILS_CORE_URL = 'https://maven.aliyun.com/repository/grails-core/'
        def ALIYUN_APACHE_SNAPSHOTS_URL = 'https://maven.aliyun.com/repository/apache-snapshots/'

        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                // central
                if (url.startsWith('https://repo1.maven.org/maven2/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_CENTRAL_URL."
                    remove repo
                }
                // jcenter
                if (url.startsWith('https://jcenter.bintray.com/') || url.startsWith('http://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_PUBLIC_URL."
                    remove repo
                }
                // google
                if (url.startsWith('https://maven.google.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_GOOGLE_URL."
                    remove repo
                }
                // gradle-plugin
                if (url.startsWith('https://plugins.gradle.org/m2/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_GRADLE_PLUGIN_URL."
                    remove repo
                }
                // spring
                if (url.startsWith('https://repo.spring.io/libs-milestone/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_SPRING_URL."
                    remove repo
                }
                // spring-plugin
                if (url.startsWith('http://repo.spring.io/plugins-release/') || url.startsWith('https://repo.spring.io/plugins-release/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_SPRING_PLUGIN_URL."
                    remove repo
                }
                // grails-core
                if (url.startsWith('https://repo.grails.org/grails/core/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_GRAILS_CORE_URL."
                    remove repo
                }
                // apache snapshots
                if (url.startsWith('https://repository.apache.org/snapshots/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_APACHE_SNAPSHOTS_URL."
                    remove repo
                }
            }
        }

        maven { url ALIYUN_CENTRAL_URL }
        maven { url ALIYUN_JCENTER_PUBLIC_URL }
        maven { url ALIYUN_GOOGLE_URL }
        maven { url ALIYUN_GRADLE_PLUGIN_URL }
        maven { url ALIYUN_SPRING_URL }
        maven { url ALIYUN_SPRING_PLUGIN_URL }
        maven { url ALIYUN_GRAILS_CORE_URL }
        maven { url ALIYUN_APACHE_SNAPSHOTS_URL }
    }
}
```

## Maven

### 单项目配置

项目配置文件`pom.xml`文件中配置镜像地址，<project>标签下添加<repositories>标签配置阿里云镜像

```xml
<repositories>
    <repository>
        <id>alimaven</id>
        <name>aliyun maven</name>
        <url>https://maven.aliyun.com/repository/public</url>
    </repository>
</repositories>
```

### 全局配置

Maven 默认配置文件地址，`Users/<PC_USER_NAME>/.m2`目录下，如果没有，则新建一个`settings.xml`文件，进行镜像的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">

    <mirrors>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云central仓库</name>
            <url>https://maven.aliyun.com/repository/central/</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云public仓库</name>
            <url>https://maven.aliyun.com/repository/public/</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云Google仓库</name>
            <url>https://maven.aliyun.com/repository/google/</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云gradle-plugin仓库</name>
            <url>https://maven.aliyun.com/repository/gradle-plugin/</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云Spring仓库</name>
            <url>https://maven.aliyun.com/repository/spring</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云spring-plugin仓库</name>
            <url>https://maven.aliyun.com/repository/spring-plugin/</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云grails-core插件仓库</name>
            <url>https://maven.aliyun.com/repository/grails-core/</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云Apache仓库</name>
            <url>https://maven.aliyun.com/repository/apache-snapshots/</url>
        </mirror>
    </mirrors>

    <proxies/>
    <profiles/>
    <activeProfiles/>
</settings>
```

>具体的配置教程，可参考[阿里云云效 maven](https://maven.aliyun.com/mvn/guide)

## Homebrew

Homebrew是 macOS 系统的一款开源的包管理器

### 修改镜像

```bash
# macOS 系统修改 homebrew 镜像地址为清华镜像
git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git
git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git
# 『可选』 如果之前有安装过 font 相关，则更改 font 相关的镜像地址，否则会提示 cask-fonts 不存在
git -C "$(brew --repo homebrew/cask-fonts)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask-fonts.git
# 『可选』 如果之前有安装过 drivers 相关，则更改 drivers 相关的镜像地址，否则会提示 cask-drivers 不存在
git -C "$(brew --repo homebrew/cask-drivers)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask-drivers.git
# 更换后测试工作是否正常
brew update
# 替换Homebrew Bottles源（以下方式二选一即可）
# 1. bash用户
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
# 2. zsh用户
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.zshrc
source ~/.zshrc
```

### 恢复镜像

```bash
# macOS 系统恢复 homebrew 原镜像地址
git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git
git -C "$(brew --repo homebrew/core)" remote set-url origin https://github.com/Homebrew/homebrew-core.git
git -C "$(brew --repo homebrew/cask)" remote set-url origin https://github.com/Homebrew/homebrew-cask.git
# 『可选』 如果之前有安装过 font 相关，则更改 font 相关的镜像地址，否则会提示 cask-fonts 不存在
git -C "$(brew --repo homebrew/cask-fonts)" remote set-url origin https://github.com/Homebrew/homebrew-cask-fonts.git
# 『可选』 如果之前有安装过 drivers 相关，则更改 drivers 相关的镜像地址，否则会提示 cask-drivers 不存在
git -C "$(brew --repo homebrew/cask-drivers)" remote set-url origin https://github.com/Homebrew/homebrew-cask-drivers.git
# 更换后测试工作是否正常
brew update
# 取消 Homebrew Bottles源设置（以下方式二选一）
# 1. bash用户，取消 HOMEBREW_BOTTLE_DOMAIN 设置
vim ~/.bash_profile
source ~/.bash_profile
# 2. zsh用户，取消 HOMEBREW_BOTTLE_DOMAIN 设置
vim ~/.zshrc
source ~/.zshrc
```

>其他镜像：[中科大](https://mirrors.ustc.edu.cn/)

## Github

### 资源文件无法加载

GitHub 上各种图片都无法加载，不仅仅是头像，包括各个仓库中的图片也是。开始以为是科学上网的问题，又或者是图片本身失效，可是验证下来都是无关的，经过一番折腾查找，原来是 DNS 被污染导致

解决方法：通过查看头像等文件的访问地址，了解到这些地址的域名都是 `githubusercontent.com`，然后通过[IP 地址查询](https://www.ipaddress.com)可以找到其对应的 IP 地址，并将其相关二级域名一起配置到 Hosts 文件中

host 路径

* macOS：/etc/
* Windows：C:\Windows\System32\drivers\etc

```bash
# Github start
140.82.114.3      github.com
140.82.112.4      gist.github.com

185.199.108.153    assets-cdn.github.com
185.199.109.153    assets-cdn.github.com
185.199.110.153    assets-cdn.github.com
185.199.111.153    assets-cdn.github.com
# *.githubusercontent.com   raw|gist|cloud|camo|avatars0-9|avatars
199.232.96.133     raw.githubusercontent.com
199.232.96.133     gist.githubusercontent.com
199.232.96.133     cloud.githubusercontent.com
199.232.96.133     camo.githubusercontent.com
199.232.96.133     avatars0.githubusercontent.com
199.232.96.133     avatars1.githubusercontent.com
199.232.96.133     avatars2.githubusercontent.com
199.232.96.133     avatars3.githubusercontent.com
199.232.96.133     avatars4.githubusercontent.com
199.232.96.133     avatars5.githubusercontent.com
199.232.96.133     avatars6.githubusercontent.com
199.232.96.133     avatars7.githubusercontent.com
199.232.96.133     avatars8.githubusercontent.com
199.232.96.133     avatars.githubusercontent.com
```

### git clone 慢的想砸电脑

#### 方式一：设置代理

这个无解，只能在<font color=red>开启代理的前提下</font>，也给终端设置代理

```bash
# 只对 github.com，这里的 port 端口，需要你本地HTTP代理的端口来设置
git config --global http.https://github.com.proxy socks5://127.0.0.1:port
# 取消代理
git config --global --unset http.https://github.com.proxy
```

#### 方式二：Gitee 中转

另一种方式，适用于你需要获取 GitHub 源码做相关的其他操作时，可以借助于 Gitee 来作为中转

![improt-github-to-gitee](https://res.cloudinary.com/incoder/image/upload/v1585097262/blog/improt-github-to-gitee.png)

可以参考大佬的手把手教你
<iframe src="//player.bilibili.com/player.html?aid=94251133&bvid=BV1aE411p7Cd&cid=160907040&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

#### 方式三：cnpmjs镜像中转

在你需要 clone 的仓库地址中，添加 `.cnpmjs.org` 在 `github.com` 的后面

![github-cnpmjs](https://res.cloudinary.com/incoder/image/upload/v1595934447/blog/github-cnpmjs.png)

#### 方法四：jsdelivr 免费 CDN 加速

适用于下载单个文件

<iframe src="//player.bilibili.com/player.html?aid=371171067&bvid=BV13Z4y1p75s&cid=205661842&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

## 参考

* [Homebrew/Linuxbrew 镜像使用帮助](https://mirror.tuna.tsinghua.edu.cn/help/homebrew/)
* [阿里云公共代理库配置指南](https://help.aliyun.com/document_detail/102512.html?spm=a2c40.aliyun_maven_repo.0.0.36183054ncuCr4)