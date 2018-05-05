---
title: Api 文档管理系统 RAP1环境搭建
date: 2018-03-27 10:19:25
categories: Api
tag: RAP
---

前后端分离的路上，一款强大的API管理工具，可以降低沟通成本，大大提高开发效率，节省的时间，我们来做更有意义的事情。

API管理工具又很多，选择适合自身需求的就是最好

这里以[阿里妈妈](https://thx.github.io)出品的[RAP](https://thx.github.io/RAP)产品；目前RAP分为： ~~[RAP1](https://github.com/thx/RAP)~~，[RAP2](https://github.com/thx/rap2-delos)

>虽然RAP1不再添加新功能，只做维护工作，介于RAP2目前还不是很成熟，本篇文章先讲RAP1的搭建过程(虽然官方[Wiki](https://github.com/thx/RAP/wiki)已经有很详细的部署教程，但在部署过程中还是遇到一些问题，因此就记录下来)

如果你不需要搭建，可以直接访问RAP1提供的服务[http://rapapi.org](http://rapapi.org)

## 项目构建

* 安装环境：Windows 10 x64
* 准备工具：[Git](https://git-scm.com/downloads)，[IDEA](https://www.jetbrains.com/idea/download)，[JDK1.8+](https://www.java.com/zh_CN/download/manual.jsp)，[Tomcat8*](https://tomcat.apache.org/download-80.cgi)，[MySQL](https://www.mysql.com/cn/downloads)，[Redis3+](https://redis.io/download)

这里Git，IDEA，JDK1.8，Tomcat8*，MySQL不再赘述安装步骤以及环境配置

### 安装基本工具
#### Redis
由于Redis 官方并未支持Windows系统，因此借助MicrosoftArchive团队所提供的[Windows Redis安装包](https://github.com/MicrosoftArchive/redis/releases)，这里下载最新的`Redis-x64-3.2.100.msi`

* 以管理员身份运行安装包`Redis-x64-3.2.100.msi`
    1. 添加环境变量  
    ![env](https://res.cloudinary.com/incoder/image/upload/v1525517089/blog/gitpages-redis-env-var.png)
    2. 默认`6379`端口  
    ![port](https://res.cloudinary.com/incoder/image/upload/v1525517270/blog/gitpages-redis-port.png)
    3. 检查Redis服务，是否已经启动  
    ![serve](https://res.cloudinary.com/incoder/image/upload/v1525517284/blog/gitpages-redis-serve.png)

    >其他默认即可，不要设置Memory Limit

## 构建项目
### 获取源代码
```sh
git clone git@github.com:thx/RAP.git
git checkout release
```

>确保您正确的切换到release分支，否则会出现少包，因为master分支引用一些不对外公开的内部组件，不提供给外部用户使用
### 导入到IDEA
IDEA==>Open==>RAP

### 初始化数据库
执行脚本文件：RAP\src\main\resources\database\\`initialize.sql`

### 修改配置文件
文件：RAP\src\main\resources\database\\`config.properties`  
修改：数据库`用户名`及`密码`  
![update](https://res.cloudinary.com/incoder/image/upload/v1525517302/blog/gitpages-rap1-update-database-config.png)
## 启动项目
1. Edit config  
![config](https://res.cloudinary.com/incoder/image/upload/v1525517326/blog/gitpages-rap1-tomcat-config.png)
2. Create Tomcat  
![create](https://res.cloudinary.com/incoder/image/upload/v1525517350/blog/gitpages-rap1-tomcat-create.png)
3. Deploy war  
![deploy](https://res.cloudinary.com/incoder/image/upload/v1525517364/blog/gitpages-rap1-tomcat-deploy.png)
4. Deploy success  
![success](https://res.cloudinary.com/incoder/image/upload/v1525517384/blog/gitpages-rap1-deploy-success.png)

注意成功部署后，请`注册`新账号登录

至此，RAP1的本机部署已经完成。

## 其他
* [RAP1学习中心](http://thx.github.io/RAP/study.html)
    部分同学无法查看视频，请异步至[issues](https://github.com/thx/RAP/issues/935)
* [RAP1 Wiki](https://github.com/thx/RAP/wiki)文档
* [Mockjs](http://mockjs.com)
* [RAP2环境搭建教程](https://www.incoder.org/2018/03/27/rap2)