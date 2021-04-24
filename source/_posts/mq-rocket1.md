---
title: MQ 系列 — RocketMQ（一）环境搭建
date: 2020-11-10 21:30:30
categories: MQ
tag: [RocketMQ]
---

本篇我们来看 MQ 系列的另一个广泛使用的中间件 [RocketMQ](https://rocketmq.apache.org)。官方介绍到 “Apache RocketMQ™ 是一个统一的消息传递引擎，轻量级的数据处理平台。Apache RocketMQ 是一个分布式消息传递和流媒体平台，具有低延迟，高性能和可靠性，万亿级容量和灵活的可伸缩性” 。更重要的是在分布式消息队列中，目前唯一提供<font color="red">完整的事务消息</font>的，只有 RocketMQ。

<!-- more -->

{% note info %}
* ~~RocketMQ 3.0.8 以及之前的版本是 支持分布式事务消息（找不到对应的提交记录）~~
* ~~RocketMQ 3.0.8 之后，分布式事务的阉割了，不支持分布式事务消息（找不到对应的提交记录）~~
* [RocketMQ 4.0.0 开始 Apache 孵化，但是也不支持分布式事务消息](https://rocketmq.apache.org/release_notes/release-notes-4.0.0-incubating/)
* [RocketMQ 4.3.0 又开始支持分布式事务消息](https://rocketmq.apache.org/release_notes/release-notes-4.3.0/)
{% endnote %}

## 基本概念

RocketMQ 由四部分组成：name servers, brokers, producers and consumers。它们中的每一个都可以在没有单个故障点的情况下进行水平扩展

### name servers

用来保存 Broker 相关 Topic 等元信息并给 Producer，提供 Consumer 查找 Broker 信息。主要包括两个功能：

1. Broker 管理，NameServer 接受来自经纪人群集的注册，并提供心跳机制以检查经纪人是否还活着
2. Routing 管理，每个NameServer 将保存有关代理群集的完整路由信息以及客户端查询的队列信息

### brokers

负责消息的存储和传递，消息查询，HA 保证等（消息存储中心，主要作用是接收来自 Producer 的消息并存储， Consumer 从这里取得消息）。Broker 服务器具有几个重要的子模块：

* Remoting Module：处理来自客户端的请求
* Client Manager：管理客户（生产者/消费者）并维护消费者的主题订阅
* Store Service：提供简单的 API，以在物理磁盘中存储或查询消息
* HA Service：提供主代理（master broker）和从代理（slave broker）之间的数据同步功能
* Index Service：通过指定的键为消息建立索引并提供快速的消息查询

### producers

负责产生消息，生产者向消息服务器发送由业务应用程序系统生成的消息。支持分布式部署，分布式生产者通过多种负载平衡模式将消息发送到 Broker 集群。发送过程支持快速失败并且延迟低

### consumers

负责消费消息，消费者从消息服务器拉取信息并将其输入用户应用程序。支持 “推和拉” 模型中的分布式部署。它还支持集群使用和消息广播。它提供了实时消息订阅机制，可以满足大多数消费者的需求

## 整体流程

## 准备工作

* Linux
* JDK8+
* Maven3.2.x+
* Git

>相关工具没安装可参考 [Linux 常用应用安装](https://incoder.org/2018/05/15/linux-build/)

## 单机部署

单机部署，主要是进行 RocketMQ 的简单使用，因此没有必要分配较大内存空间，RocketMQ NameServer 默认会占用 **4G**，因此在启动部署时会调整 JVM 的相关参数，指定分配内存空间

### 普通部署

#### RocketMQ 部署

1. Nameserver
    ```bash
    # 程序存放位置，根据喜好
    cd /home/application
    # 下载应用
    wget https://archive.apache.org/dist/rocketmq/4.7.1/rocketmq-all-4.7.1-bin-release.zip
    # 解压文件，并进入解压后的目录，进行查看目录概要等信息(没有 unzip 命令，请 yum install unzip)
    unzip rocketmq-all-4.7.1-bin-release.zip && cd rocketmq-all-4.7.1-bin-release/ && ls -l
    # 进入启动目录
    cd bin/

    # 编辑启动脚本文件，修个相应的 JVM 参数
    vim runserver.sh
    ### 定位到: JAVA_OPT="${JAVA_OPT} -server -Xms4g -Xmx4g -Xmn2g -XX:MetaspaceSize=1 28m -XX:MaxMetaspaceSize=320m"
    ### 更改为: JAVA_OPT="${JAVA_OPT} -server -Xms512M -Xmx512M -Xmn256M -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=320m"
    
    # 修个完成后启动 nameserver 应用
    nohup ./mqnamesrv &
    ```
2. 启动 broker
    ```bash
    # 进入 bin 目录
    cd /home/application/rocketmq-all-4.7.1-bin-release/bin/

    # 编辑启动脚本文件，修个相应的 JVM 参数
    vim runbroker.sh
    ### 定位到: JAVA_OPT="${JAVA_OPT} -server -Xms8g -Xmx8g -Xmn4g"
    ### 更改为: JAVA_OPT="${JAVA_OPT} -server -Xms1g -Xmx1g -Xmn512m"

    # 修个完成后，后台启动 broker，-n 指定 NameServer 服务ip地址
    nohup ./mqbroker -n localhost:9876 &
    ```
3. 验证 RocketMQ
    ```bash
    # 使用 clusterList 命令来查看集群的状态
    sh /home/application/rocketmq-all-4.7.1-bin-release/bin/mqadmin clusterList -n 127.0.0.1:9876
    ```

#### RocketMQ-Console 部署

通过命令去操作 RocketMQ，其实是比较麻烦，没有图形化来的直观和方法。为此 RocketMQ 官方提供了一个运维管理界面 RokcetMQ-Console-Ng，用于对 RocketMQ 集群提供常用的运维功能

>基于 SpringBoot 开发

```bash
wget https://github.com/apache/rocketmq-externals/archive/rocketmq-console-1.0.0.tar.gz
tar -xf rocketmq-console-1.0.0.tar.gz
# 重命名，为了方便后续操作
mv rocketmq-externals-rocketmq-console-1.0.0/rocketmq-console rocketmq-consoe
cd rocketmq-console

# 编辑配置文件
vim src/main/resources/applications.properties
### 修改指向的 nameserver 地址
### rocketmq.config.namesrvAddr=127.0.0.1:9876

# 使用 maven 命令编译源代码
mvn clean package -DskipTests
# 复制包到自己常用的软件安装目录
cp rocketmq-console-ng-1.0.0.jar /opt/application/
# 启动 rocketmq-conolse
nohup java -jar rocketmq-console-ng-1.0.0.jar &
```

正常启动后，访问：http://localhost:8080 查看是否安装成功

{% note info %}
如果你使用的 root 用户启动 rocketmq, rocketmq-console 应用，那么他们的日志分别在
* rocketmq: /home/root/logs/rocketmqlogs/
* rocketmq-console: /home/root/logs/consolelogs
{% endnote %}

### Docker 部署

截止 2020-11-10，官方的镜像依然还是 4.6 版本，难道又是阿里没人维护的 KPI 🙄

[RocketMQ-Docker](https://github.com/apache/rocketmq-docker)

## 分布式部署

### 普通部署

### Docker 部署

## 参考

1. [《Apache RocketMQ 从入门到实战》.pdf](https://developer.aliyun.com/ebook/ranking)
2. [芋道 RocketMQ 极简入门](https://www.iocoder.cn/RocketMQ/install/)
3. [芋道 Spring Boot 消息队列 RocketMQ 入门](https://www.iocoder.cn/Spring-Boot/RocketMQ/)
4. [RocketMQ 4.7.1 环境搭建、集群、SpringBoot整合MQ](https://www.cnblogs.com/chenyanbin/p/13798952.html)