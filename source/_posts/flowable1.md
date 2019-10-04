---
title: Flowable（一）初识
date: 2019-09-25 12:40:46
categories: Flowable
tag: [Flowable]
---

Flowable是一个使用Java编写的轻量级业务流程引擎。Flowable流程引擎可用于部署BPMN 2.0流程定义（用于定义流程的行业XML标准）， 创建这些流程定义的流程实例，进行查询，访问运行中或历史的流程实例与相关数据等，众所周知，Flowable是Activit的一个分叉，[Flowable的第一个版本（5.22.0）是基于Activit（5.21.0）](https://blog.flowable.org/2016/10/13/flowable-5-22-0-release/)，关于为什么Flowable会从Activit分叉，感兴趣可以查看Flowable官方的文章[Flowable and Activiti: What the Fork?!](https://blog.flowable.org/2016/10/12/flowable-and-activiti-what-the-fork/)，这里不在赘述这些内容

从[Flowable官方文档](https://www.flowable.org/documentation.html)介绍，可知Flowable遵循[BPMN](https://www.flowable.org/docs/userguide/index.html)，[CMMN](https://www.flowable.org/docs/userguide-cmmn/index.html)，[DMN](https://www.flowable.org/docs/userguide-dmn/index.html)，[From](https://www.flowable.org/docs/userguide-form/index.html)设计指导
* BPMN：用于流程管理
* CMMN：用于案例管理
* DMN：用于决策规则
* Form：用于表单和任务表单管理

## Flowable 直运行

>这里所说的"**直运行**"，是指不需要写任何代码，仅需要改动相关的配置就可以运行起Flowable应用程序

准备工作
* [Flowable v6.4.2](https://www.flowable.org/downloads.html)
* MySQL8+
* JDK & Tomcat 环境

> MySQL8+ ，JDK，Tomcat环境代建可参考[Linux 之 MySQL](https://incoder.org/2018/07/23/linux-mysql/)，[Linux 常用应用安装](https://incoder.org/2018/05/15/linux-build/)，[Windows 之 常用应用安装](https://incoder.org/2019/09/25/windows-devtool/)

已下操作均在Windows上，macOS上相差不大，操作流程基本一致

### war部署

1. 解压flowable.zip文件
    ![flowable-zip](https://res.cloudinary.com/incoder/image/upload/v1569565014/blog/flowable-zip.png)
2. 拷贝需要启动的war到安装的Tomcat的`webapps`路径下
    ![flowable-tomact](https://res.cloudinary.com/incoder/image/upload/v1569554378/blog/flowable-tomact.png)
3. 命令行中执行`startup.bat`命令，或执行Tomcat的`bin`路径下，启动`startup.bat`文件
    ![flowable-startup](https://res.cloudinary.com/incoder/image/upload/v1569554648/blog/flowable-startup.png)
4. 第一次启动，Tomcat控制台应该会出错，因为`flowable-admin.war`数据库配置默认使用H2数据库，我们需要修改数据库配置连接等信息
    ![flowable-mysql-config](https://res.cloudinary.com/incoder/image/upload/v1569555241/blog/flowable-mysql-config.png)
    >* 文件地址：`<your tomcat path>/webapps/flowable-admin/WEB-INF/classes`路径，`flowable-default.properties`文件及`application-dev.properties`文件
    >* MySQL中需要一个名为 **flowable** 的数据库，没有请创建一个`CREATE DATABASE flowable`
    >* 由于我使用的是 MySQL8 ，Tomcat 中不包含此驱动 jar 包，因此需要手动下载[mysql-connector-java-8.x.x(和你数据库匹配版本).zip](http://ftp.jaist.ac.jp/pub/mysql/Downloads/Connector-J/)文件进行解压，拷贝`mysql-connector-java-8.x.x.jar`文件到 `<your tomcat path>/lib`路径下
5. 重新在命令行中执行`startup.bat`命令，或执行Tomcat的`bin`路径下，启动`startup.bat`文件
6. 正常情况到此等待服务器启动完成，如果不能正常启动，请查看Tomcat控制台是否有错误，按照提示解决错误，直到Tomcat不再有错误提示即可


### 使用

1. 访问[http://localhost:8080/flowable-idm](http://localhost:8080/flowable-idm)，默认账号：admin，默认密码：test
    ![flowable-admin](https://res.cloudinary.com/incoder/image/upload/v1569563245/blog/flowable-admin.png)
2. 访问[http://localhost:8080/flowable-admin](http://localhost:8080/flowable-admin)，后台管理
3. 访问[http://localhost:8080/flowable-modeler](http://localhost:8080/flowable-modeler)，流程定义管理
4. 访问[http://localhost:8080/flowable-task](http://localhost:8080/flowable-task)，用户任务管理
5. 访问[http://localhost:8080/flowable-rest/docs](http://localhost:8080/flowable-rest/docs)，流程引擎对外提供的API接口

## Flowable 集成运行

>这里所说的"**集成运行**"，是指通过Flowable官方提供的jar文件，集成到我们的项目中运行的方式

## Flowable 使用

## 其他

### 如何切换中文

Flowable中已包含中文语言，会根据操作系统语言，自动显示对应语言

### startup.bat异常

查看控制它异常，例如当前flowable启动默认端口8080，被占用
![flowable-aleady-bind](https://res.cloudinary.com/incoder/image/upload/v1569556130/blog/flowable-aleady-bind.png)

解决方法：查找占用端口进程`netstat -ano|findstr 端口号`，并kill它`taskkill -PID 进程号 -F`
![flowable-kill-task](https://res.cloudinary.com/incoder/image/upload/v1569556500/blog/flowable-kill-task.png)