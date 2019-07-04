---
title: IDEA 多模块项目
date: 2019-01-10 00:32:10
categories: IDEA
tag: [SpringBoot, Init]
---

[Jetbrains](https://www.jetbrains.com)系列中[IDEA](https://www.jetbrains.com/idea)是现如今公认最好用，最强大的Java开发工具，不接受任何反驳，本篇介绍macOS上使用 IDEA 创建 SpringBoot 多模块项目

## 准备工作
* 系统环境：macOS 10.14.2
* 应用工具：[IDEA](https://www.jetbrains.com/idea)，[Maven](https://maven.apache.org)

>这里不再介绍基本软件的安装及配置

## 多模块项目

一般简单的项目，按照如下项目结构进行构建，可根据也无需要自行调整
```
rc-springboot-docker
  ├── boot-api           # 项目对应用服务间提供api的接口，同时也管理项目常量、REST返回组装实体类等
  ├── boot-common        # 项目公共基础包（可丢弃）
  ├── boot-core          # 项目业务操作，server dao层
  ├── boot-web           # 项目后端Web管理
  ├── boot-rest          # 项目业务控制层，给客户端提供rest接口
  └── README.md
```

* boot-api：是一个maven module
* boot-common：是一个maven module
* boot-core：是一个maven module
* boot-web：是一个springboot module
* boot-rest：是一个springboot module

## 构建

### Parent Project
顾名思义，这是项目的外壳，一个标准的empty maven project，当然你要可以使用gradle来作为项目的构建工具，可根据自身需要自行选择，这里采用maven方式演示
* `Create Project`
    ![idea-new-project](https://res.cloudinary.com/incoder/image/upload/v1547062782/blog/idea-new-project.png)
* 设置项目groupId和artifactId等信息
    ![idea-new-setting](https://res.cloudinary.com/incoder/image/upload/v1547062780/blog/idea-new-setting.png)
* 设置项目名称及项目存储位置
    ![idea-new-path](https://res.cloudinary.com/incoder/image/upload/v1547062780/blog/idea-new-path.png)
* 删除项目src目录，使项目成为名副其实的空项目
    ![idea-delete-src](https://res.cloudinary.com/incoder/image/upload/v1547062780/blog/idea-delete-src.png)
* 新增忽略文件
    ![idea-new-ignore](https://res.cloudinary.com/incoder/image/upload/v1547062782/blog/idea-new-ignore.png)
    新增忽略文件的目的：
    1. 忽略项目中不需要进行版本追踪的文件
    2. 隐藏忽略文件

* 选择maven项目模板忽略文件
    ![idea-select-maven](https://res.cloudinary.com/incoder/image/upload/v1547062782/blog/idea-select-maven.png)
* 修改忽略文件及隐藏忽略文件
    ![idea-ignore-settings](https://res.cloudinary.com/incoder/image/upload/v1547062781/blog/idea-ignore-settings.png)
    ```
    # IntelliJ project files
    .DS_Store
    .idea/
    *.iml
    out
    gen

    # eclipse
    *.classpath
    *.project
    *.springBeans
    ```
    >关于ignore文件的写法，可以参考[.gitignore 基础知识](https://incoder.org/2018/04/13/gitignore/)

### Module Project
在module中有两类，一类是maven项目，还有一类是需要启动的springboot项目

#### maven module project

* 创建maven module
    ![idea-module-maven](https://res.cloudinary.com/incoder/image/upload/v1547066735/blog/idea-module-maven.png)
* 设置maven module artifactId等信息
    ![idea-module-maven-artifact](https://res.cloudinary.com/incoder/image/upload/v1547066737/blog/idea-module-maven-artifact.png)
* 设置maven module 名称及存储位置
    ![idea-module-maven-name](https://res.cloudinary.com/incoder/image/upload/v1547066735/blog/idea-module-maven-name.png)
#### springboot module project

* 创建springboot module
    ![idea-new-module-springboot](https://res.cloudinary.com/incoder/image/upload/v1547066076/blog/idea-new-module-springboot.png)
* 设置springboot module 信息
    ![idea-module-metadata](https://res.cloudinary.com/incoder/image/upload/v1547066075/blog/idea-module-metadata.png)
* 选择核心组件
    ![idea-module-springboot-core](https://res.cloudinary.com/incoder/image/upload/v1547066076/blog/idea-module-springboot-core.png)
* 设置springboot module 名称及存储位置
![idea-module-springboot-name](https://res.cloudinary.com/incoder/image/upload/v1547066076/blog/idea-module-springboot-name.png)

### Modify Config

#### Modify parent pom
