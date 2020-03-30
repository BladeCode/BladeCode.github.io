---
title: 专治各种网络不服 
date: 2020-02-27 15:00:10
categories: DevTool
tag: [Exp]
---

众所周知国内的开发已经由原来的“致敬”到现在软件生态领域的“引领”（目前来说还未到真正引领）世界技术发展，但是一个问题始终还是未能有所突破，作为中国的开发者每次在面对新的技术在环境搭建就劝退了一众人，很多开发所依赖的项目资源都来自国外服务，而由于中国的特殊，对很多国外服务的限制，让你在开始的第一阶段总是碰的鼻青脸肿，把大量的时间浪费在环境搭建的等待上，虽然现在很多开源组织或者一线大厂提供了相应的镜像服务方便国内的开发者，但很多都还需要我们自行去更改或者解决这些问题，本篇文章就是我的开发之路上的各种网络问题的解决办法

<!-- more -->

## Gradle

Gradle 作为新一代的包管理工具，早期作为 Android 项目的御用包管理，渐渐的越来越多的服务端项目也开始在使用 Gradle 来进行管理，至于 Gradle 和 Maven 的对比，我这里不做评论，请移步 Gradle 官方网站对两个包管理的比较[Gradle vs Maven Comparison](https://gradle.org/maven-vs-gradle/)

> Gradle 镜像地址：https://services.gradle.org/distributions

### 单项目配置

#### Android 项目

```groovy
buildscript {
    repositories {
        // 添加或修改这里指向的镜像仓库地址
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
        // 添加或修改这里指向的镜像仓库地址
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
    // 显式指定仓库访问地址，以下两个地址推荐阿里云镜像
   maven {
       // 指向 spring 官方仓库
       url 'http://repo.spring.io/release',
       // 指向 阿里云 镜像仓库
       url 'https://maven.aliyun.com/repository/public'
   }
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

### 全局配置

在当前系统 `${USER_HOME}/.gradle/` 目录下创建 `init.gradle` 文件，将 Maven 和 Jcenter 仓库都指向阿里云镜像仓库

>这里没有完全去匹配，只列举了 maven，jcenter，google 仓库映射的更改，其他使用到的各位自行添加

```groovy
allprojects{
    repositories {
        def ALIYUN_REPOSITORY_URL = 'https://maven.aliyun.com/repository/public'
        def ALIYUN_JCENTER_URL = 'https://maven.aliyun.com/repository/jcenter'
        def ALIYUN_GOOGLE_URL = 'https://maven.aliyun.com/repository/google'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
                    remove repo
                }
                if (url.startsWith('https://maven.google.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_GOOGLE_URL."
                    remove repo
                }
            }
        }
        maven {
            url ALIYUN_REPOSITORY_URL
            url ALIYUN_JCENTER_URL
            url ALIYUN_GOOGLE_URL
        }
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

Maven 默认配置文件地址，`Users/<PC_USER_NAME>/.m2`目录下，如果没有，则新建一个`setting.xml`文件，进行镜像的配置

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
            <name>阿里云公共仓库</name>
            <url>https://maven.aliyun.com/repository/public</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云Google仓库</name>
            <url>https://maven.aliyun.com/repository/google</url>
        </mirror>
        <mirror>
            <id>aliyunmaven</id>
            <mirrorOf>*</mirrorOf>
            <name>阿里云Apache仓库</name>
            <url>https://maven.aliyun.com/repository/apache-snapshots</url>
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
            <name>阿里云Spring插件仓库</name>
            <url>https://maven.aliyun.com/repository/spring-plugin</url>
        </mirror>
    </mirrors>

    <proxies/>
    <profiles/>
    <activeProfiles/>
</settings>
```

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

### 常用命令

```shell
# 安装指定包应用
brew install [包名]
# 卸载指定包
brew uninstall [包名]
# 列出已安装的软件
brew list
# 更新brew
brew update
# 用浏览器打开brew的官方网站
brew home
# 显示软件信息
brew info
# 显示包依赖
brew deps
# 更新所有
brew upgarde
# 更新指定包
brew upgarde [包名]
# 清理所有包的旧版本
brew cleanup
# 清理指定包的旧版本
brew cleanup [包名]
# 查看可清理的旧版本包，不执行实际操作
brew cleanup -n
# 查找指定包
brew search [包名]
```

## Github

### 资源文件无法加载

GitHub 上各种图片都无法加载，不仅仅是头像，包括各个仓库中的图片也是。开始以为是科学上网的问题，又或者是图片本身失效，可是验证下来都是无关的，经过一番折腾查找，原来是 DNS 被污染导致

解决方法：通过查看头像等文件的访问地址，了解到这些地址的域名都是 `githubusercontent.com`，然后通过[IP 地址查询](https://www.ipaddress.com)可以找到其对应的 IP 地址，并将其相关二级域名一起配置到 Hosts 文件中

host 路径
* macOS：/etc/
* Windows：C:\Windows\System32\drivers\etc

```
199.232.28.133 gist.githubusecontent.com
199.232.28.133 user-images.githubusercontent.com
199.232.28.133 raw.githubusercontent.com
199.232.28.133 camo.githubusercontent.com
199.232.28.133 cloud.githubusercontent.com
199.232.28.133 avatars0.githubusercontent.com
199.232.28.133 avatars1.githubusercontent.com
199.232.28.133 avatars2.githubusercontent.com
199.232.28.133 avatars3.githubusercontent.com
199.232.28.133 avatars4.githubusercontent.com
199.232.28.133 avatars5.githubusercontent.com
199.232.28.133 avatars6.githubusercontent.com
199.232.28.133 avatars7.githubusercontent.com
199.232.28.133 avatars8.githubusercontent.com
```

### git clone 慢的想砸电脑

#### 方式一，设置代理

这个无解，只能在<font color=red>开启代理的前提下</font>，也给终端设置代理

```bash
# 只对 github.com，这里的 port 端口，需要你本地HTTP代理的端口来设置
git config --global http.https://github.com.proxy socks5://127.0.0.1:port
# 取消代理
git config --global --unset http.https://github.com.proxy
```

#### 方式二，Gitee 中转

另一种方式，适用于你需要获取 GitHub 源码做相关的其他操作时，可以借助于 Gitee 来作为中转

![improt-github-to-gitee](https://res.cloudinary.com/incoder/image/upload/v1585097262/blog/improt-github-to-gitee.png)

可以参考大佬的手把手教你
<iframe src="//player.bilibili.com/player.html?aid=94251133&bvid=BV1aE411p7Cd&cid=160907040&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

## 参考

* [Homebrew/Linuxbrew 镜像使用帮助](https://mirror.tuna.tsinghua.edu.cn/help/homebrew/)
* [阿里云公共代理库配置指南](https://help.aliyun.com/document_detail/102512.html?spm=a2c40.aliyun_maven_repo.0.0.36183054ncuCr4)