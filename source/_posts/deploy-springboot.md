---
title: IDEA 之 SpringBoot 应用部署
date: 2019-11-19 20:32:10
categories: IDEA
tag: [SpringBoot, Deploy]
---

服务端由原来 **混合式**（Java+JSP）的方式演进成了专注于提供服务 API（**前后端分离**）的方式，开发的明确分工，各自开发人员在各自领域的垂直技能的加强，以满足业务的快速迭代，因此也就在这两个方式中，项目的构建方式也有了一定的变化，混合模式中常编译为 war 包，而在前后端分离模式中常编译为 jar 包，这两种文件格式虽然都是一种压缩文件的格式，但实质还是有一些区别，那首先让我们来了解这两种文件它们之间的区别

<!-- more -->

jar 文件与 war 文件的区别
1. war 文件常应用在 **混合式** 的项目中，war 文件包含包含Java 相关的项目文件、部署文件，还包含一些前台页面等引用的相关资源文件；jar 文件常应用在 **前后端分离** 的项目中，jar 文件主要包含Java 相关的项目文件、部署文件
2. war 文件中不包含 Tomcat相关文件，必须运行在 Tomcat 容器中；jar 文件中包含了 Tomcat 文件，可直接运行
3. war 文件通常使用 SSM 架构；jar 文件通常使用 SpringBoot/SpringCloud 架构
4. 无论是 jar 还是 war 都能够使用嵌套容器，java -jar来独立运行
5. 只有 war 才能部署到外部容器中；SpringBoot支持多种模板引擎，但JSP 只能在 war 中使用

## 准备

* System：macOS
* Java：JDK 1.8+
* 编辑器：IDEA
* 包管理：maven/gradle
* 服务器连接：iTerm2
* 项目示例：
  * [rc-cluster-springboot](https://github.com/RootCluster/rc-cluster-springboot)，这是一个 gradle 管理的 SpringBoot 项目
  * [rc-ssm](https://github.com/RootCluster/rc-ssm/tree/example)，这是一个 maven 管理的 SSM 项目

>Windows 连接服务器工具可使用 [Xshell](https://www.netsarang.com/zh/xshell) 等代替

## 编译

### 编译 war

```xml
<!-- pom.xml 文件中要指明构建文件的类型 -->
<packaging>war</packaging>
```

```bash
# clean依赖并编译成 package，也可以执行 mvn clean package -DskipTests
mvn clean package -Dmaven.test.skip=true
```

![deploy-maven](https://res.cloudinary.com/incoder/image/upload/v1574837752/blog/deploy-maven.png)

### 编译 jar（SpringBoot）

#### maven

```xml
<!-- pom.xml 文件中要指明构建文件的类型 -->
<packaging>jar</packaging>
```

##### GUI 操作

##### 命令行 操作

#### gradle

##### GUI 操作

![deploy-gradle](https://res.cloudinary.com/incoder/image/upload/v1574838561/blog/deploy-gradle.png)

##### 命令行 操作

在需要编译的项目路径下，这里编译子模块springboot-start，前提需要在系统的环境变量中配置好 gradle，macOS 可参考[MacBook Pro 初始化](https://incoder.org/2018/11/10/mac-init/#Gradle配置)文章

```bash
# linux or macOS
gradle build
# Windows
./gradle build
```

![deploy-gradle-build](https://res.cloudinary.com/incoder/image/upload/v1574959663/blog/deploy-gradle-build-success.png)

## 部署

部署步骤：
1. 备份服务器对应服务，并停止服务运行（如果是第一次部署该服务，可省略）
2. 上传应用 jar/war 文件
3. 启动上传应用

```bash
# 拷贝本地文件到指定服务器的指定目录
# root：服务器用户名
# ip：服务器地址
# :/data/app：拷贝文件到服务器的/data/app 路径下
scp ~/Desktop/start-1.0-SNAPSHOT.jar root@ip:/data/app
```

### 部署 war

### 部署 jar

#### iTerm2 部署

```bash
# ssh 连接到服务器后，启动应用，如果有需要请先停止已在运行的程序
# 查看程序的进程
ps -ef|grep 'java -jar'
# UID      PID  PPID  C STIME TTY        TIME CMD
# root    6760  7103  0 19:59 pts/3  00:00:00 java -jar /data/app/start-1.0-SNAPSHOT.jar

# 终止程序进程
kill -9 pid

# nohup：后台运行程序，也就说当控制台终止，并不会停止启动的服务
# spring.log：当前目录下输出日志记录的文件名
nohup java -jar start-1.0-SNAPSHOT.jar > spring.log 2>&1 &

# 滚动查看日志输出
tail -f spring.log

# 如果不需要查看输入出的日志，运行如下脚本
nohup java -jar start-1.0-SNAPSHOT.jar &
```

tail 命令
* 命令格式：tail[必要参数][选择参数][文件]
* 功能描述：用于显示指定文件末尾内容，不指定文件时，作为输入信息进行处理；查看日志文
* 示例：tail -f spring.log
* 命令参数
  * -f 循环读取
  * -q 不显示处理信息
  * -v 显示详细的处理信息
  * -c<数目> 显示的字节数
  * -n<行数> 显示行数
  * –pid=PID 与-f合用,表示在进程ID,PID死掉之后结束.
  * -q, –quiet, –silent 从不输出给出文件名的首部
  * -s, –sleep-interval=S 与-f合用,表示在每次反复的间隔休眠S秒

#### Alibaba Cloud Toolkit

#### Jenkins

关于 Jenkins 的部署，会单独出一篇相关的部署介绍，以及 Jenkins 的相关知识介绍，请移步[使用 Jenkins 项目部署]()

## 问题

### gradle 编译失败

![deploy-gradle-build-error](https://res.cloudinary.com/incoder/image/upload/v1574960394/blog/deploy-gradle-build-error.png)

原因：这里的提示`spring-boot-2.1.6.RELEASE.jar`文件无法正常下载
解决办法：切换你的网络，或者修改 gradle 仓库镜像地址

### 缓存文件未下载完全

![](https://res.cloudinary.com/incoder/image/upload/v1574961213/blog/deploy-gradle-build-error2.png)

原因：文件未下载完全，编译时使用了缓存文件
解决办法：删除`~/.gradle/caches/modules-2/files-2.1`路径下无法编译通过的包，这里是`spring-boot-gradle-plugin`

## 附录

* [SpringBoot入门系列(四)---Spring Boot 项目打包运行](https://www.lixueduan.com/posts/144a69f9.html)