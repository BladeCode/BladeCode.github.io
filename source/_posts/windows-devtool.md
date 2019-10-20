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

## MySQL

* [官方下载地址](https://dev.mysql.com/downloads/)
* 下载`MySQL Community Server`或者`MySQL Installer for Windows`都可以，这里我下载的是`MySQL Community Server`
    ![](https://res.cloudinary.com/incoder/image/upload/v1569640711/blog/windows-mysql-download.png)

安装步骤请看截图所示
![windows-mysql-install](https://res.cloudinary.com/incoder/image/upload/v1569640478/blog/windows-mysql-install.png)

## Tomcat

[官方下载地址](http://tomcat.apache.org/)，选择需要的版本下载

### 安装

Tomcat 和 JDK 一样为了方便使用都需要配置环境变量，右击`我的电脑`-->`属性`-->`高级系统设置`-->`高级`-->`环境变量`
1. 在系统变量里新建 **`CATALINA_BASE`** 变量，变量值为你的Tomcat路径，比如：C:\Program Files\apache-tomcat-9.0.26
2. 在系统变量里新建 **`CATALINA_HOME`** 变量，变量值为你的Tomcat路径，比如：C:\Program Files\apache-tomcat-9.0.26
3. 找到 **`path`** 变量（已存在不用新建）添加变量值
    ```bash
    ;%CATALINA_HOME%\lib;%CATALINA_HOME%\bin;
    ```
4. 保存修改，并启动 CMD 进行验证，输出安装的 Java 版本表示安装成功
    ```bash
    statrup
    ```
>变量值之间用 **`;`** 隔开。注意原来Path的变量值末尾有没有 **`;`** 号，如果没有，先输入 **`;`** 号再输入

### 配置

#### 乱码

Tomcat 控制台中中文乱码，需要修改 Tomcat 配置文件 **`logging.properties`** 中的字符编码，将默认的 **UTF-8** 改为 **GBK**，文件路径`<You Tomcat>\conf`
![windows-tomcat-encode](https://res.cloudinary.com/incoder/image/upload/v1569644342/blog/windows-tomcat-encode.png)

#### 缓存

文件路径`<You Tomcat>\conf`，修改 **`context.xml`** 文件，在 `<Context>` 标签中添加如下配置即可
```xml
<Resources cachingAllowed="true" cacheMaxSize="100000" />
```

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
