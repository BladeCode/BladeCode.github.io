---
title: Windows 之 常用应用安装
date: 2019-09-25 21:08:10
categories: Windows
tag: DevTool
---

这是一篇在Windows系统下，持续更新常用开发软件安装汇总，当然一些简单得安装就在这里记录，不废话了

## JDK

[官方下载地址](https://www.oracle.com/technetwork/java/javase/downloads/index.html)，选择需要的版本下载安装包

安装完成，设置环境变量，右击`我的电脑`-->`属性`-->`高级系统设置`-->`高级`-->`环境变量`

<!-- more -->

1. 在系统变量里新建 **`JAVA_HOME`** 变量，变量值为你的JDK的安装路径，比如：C:\Program Files\Java\jdk1.8.0_60
2. 在系统变量里新建 **`CLASSPATH`** 变量
    ```bash
    .;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar
    ```
3. 找到 **`path`** 变量（已存在不用新建）添加变量值
    ```bash
    %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin
    ```
4. 保存修改，并启动 CMD 进行验证，输出安装的 Java 版本表示安装成功
    ```bash
    java -version
    ```
>变量值之间用 **`;`** 隔开。注意原来Path的变量值末尾有没有 **`;`** 号，如果没有，先输入 **`;`** 号再输入

## Git

* [官方下载地址](https://git-scm.com/downloads)
* [清华镜像下载地址](https://mirrors.tuna.tsinghua.edu.cn/github-release/git-for-windows/git/)

关于 Git 的安装没有什么可说的（使用默认配置即可），基本上就是下一步，下一步，到完成

## MySQL

* [官方下载地址](https://dev.mysql.com/downloads/)
* 下载`MySQL Community Server`或者`MySQL Installer for Windows`都可以，这里我下载的是`MySQL Community Server`
    ![](https://res.cloudinary.com/incoder/image/upload/v1569640711/blog/windows-mysql-download.png)

安装步骤请看截图所示
![windows-mysql-install](https://res.cloudinary.com/incoder/image/upload/v1569640478/blog/windows-mysql-install.png)

## Tomcat

[官方网站](http://tomcat.apache.org/)，选择需要的版本下载

* 官方 Tomcat 镜像：https://downloads.apache.org/tomcat/
* 北京理工大学：https://mirrors.bit.edu.cn/apache/tomcat/
* 北京外国语大学：https://mirrors.bfsu.edu.cn/apache/tomcat/
* 清华大学：https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/

### 安装

下载对应版本的文件，这里比如 `apache-tomcat-9.0.26` 版本文件，放入你系统某个位置，最好是英文路径且路径中没有空格或特殊字符，并解压文件

### 配置

Tomcat 和 JDK 一样为了方便使用都需要配置环境变量，右击`我的电脑`-->`属性`-->`高级系统设置`-->`高级`-->`环境变量`
1. 在系统变量里新建 **`CATALINA_BASE`** 变量，变量值为你的Tomcat路径，比如：D:\DevTools\apache-tomcat-9.0.26
2. 在系统变量里新建 **`CATALINA_HOME`** 变量，变量值为你的Tomcat路径，比如：D:\DevTools\apache-tomcat-9.0.26
3. 找到 **`path`** 变量（已存在不用新建）添加变量值
    ```bash
    ;%CATALINA_HOME%\lib;%CATALINA_HOME%\bin;
    ```
    >变量值之间用 **`;`** 隔开。注意原来Path的变量值末尾有没有 **`;`** 号，如果没有，先输入 **`;`** 号再输入
4. 保存修改，并启动 CMD 进行验证
    ```bash
    statrup
    ```

#### 乱码

Tomcat 控制台中中文乱码，需要修改 Tomcat 配置文件 **`logging.properties`** 中的字符编码，将默认的 **UTF-8** 改为 **GBK**，文件路径`<You Tomcat>\conf`
![windows-tomcat-encode](https://res.cloudinary.com/incoder/image/upload/v1569644342/blog/windows-tomcat-encode.png)

#### 缓存

文件路径`<You Tomcat>\conf`，修改 **`context.xml`** 文件，在 `<Context>` 标签中添加如下配置即可
```xml
<Resources cachingAllowed="true" cacheMaxSize="100000" />
```

## Maven

[官方网站](http://maven.apache.org)

* 官方 Maven 镜像：https://downloads.apache.org/maven/
* 北京理工大学：https://mirrors.bit.edu.cn/apache/maven/
* 北京外国语大学：https://mirrors.bfsu.edu.cn/apache/maven/
* 清华大学：https://mirrors.tuna.tsinghua.edu.cn/apache/maven/

### 安装

下载对应版本的文件，这里比如 `apache-maven-3.6.3` 版本文件，放入你系统某个位置，最好是英文路径且路径中没有空格或特殊字符，并解压文件

### 配置

Maven 和 JDK 一样为了方便使用都需要配置环境变量，右击`我的电脑`-->`属性`-->`高级系统设置`-->`高级`-->`环境变量`
1. 在系统变量里新建 **`MAVEN_HOME`** 变量，变量值为你的Tomcat路径，比如：D:\DevTools\apache-maven-3.6.3
2. 找到 **`path`** 变量（已存在不用新建）添加变量值
    ```bash
    ;%MAVEN_HOME%\bin;
    ```
    >变量值之间用 **`;`** 隔开。注意原来Path的变量值末尾有没有 **`;`** 号，如果没有，先输入 **`;`** 号再输入
3. 保存修改，并启动 CMD 进行验证
    ```bash
    mvn -version
    ```

### 镜像地址（可选）

如果你服务所依赖的包都是使用公司内部的私服，或者需要加快依赖的同步速度，那么建议你修改 maven 同步镜像的配置，编辑 `<You maven>\conf` 路径下 `settings.xml` 文件，在 <mirrors> 标签下修改镜像地址

```xml
<mirrors>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云central仓库</name>
        <url>https://maven.aliyun.com/repository/central</url>
    </mirror>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云jcenter-public仓库</name>
        <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云google仓库</name>
        <url>https://maven.aliyun.com/repository/google</url>
    </mirror>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云gradle-plugin仓库</name>
        <url>https://maven.aliyun.com/repository/gradle-plugin</url>
    </mirror>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云spring仓库</name>
        <url>https://maven.aliyun.com/repository/spring</url>
    </mirror>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云spring-plugin插件仓库</name>
        <url>https://maven.aliyun.com/repository/spring-plugin</url>
    </mirror>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云grails-core插件仓库</name>
        <url>https://maven.aliyun.com/repository/grails-core</url>
    </mirror>
    <mirror>
        <id>aliyunmaven</id>
        <mirrorOf>*</mirrorOf>
        <name>阿里云apache-snapshots仓库</name>
        <url>https://maven.aliyun.com/repository/apache-snapshots</url>
    </mirror>
</mirrors>
```

### 本地依赖存放地址

默认，maven 的依赖都存放在 `C:Users/<Your name>/.m2/reposittory`  路径下，你可以指定 maven 同步的依赖存默认放在你设置的路径下，编辑 `<You maven>\conf` 路径下 `settings.xml` 文件，修改<localRepository> 标签的内容

```xml
<localRepository>D:DevTools/maven/repository</localRepository>
```

### 配置修改验证

配置完成，在命令行输入 `mvn help:system` 测试，查看下载链接里面是否是现在配置的镜像地址，以及下载后的文件是否存放在自定设置的目标地址

## CMD

### 常用命令

1. IP地址
   ```bash
   ipconfig
   ```
2. 查看端口
    ```bash
    # 查看端口号：netstat -ano | findstr 端口号
    netstat -ano | findstr 8080
    # 查看占用端口号进程 ：tasklist | findstr 进程号
    tasklist | findstr 12836
    # kill 指定进程：taskkill -PID 进程号 -F 
    taskkill -PID 12836 -F
    ```
    截图如下：  
    ![windows-task](https://res.cloudinary.com/incoder/image/upload/v1569655372/blog/windows-task.png)
