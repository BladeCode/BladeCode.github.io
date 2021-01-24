---
title: Linux 之 MySQL
date: 2018-07-23 22:30:10
categories: Linux
tag: MySQL
---

之前粗略的接触了Linux的基础使用和安装，这次准备在自购的服务器上跑些应用，纯属娱乐，废话不说，上来就先仍数据库。
数据库常用的`Oracle`,`MySQL`,`SQL Server`,`MongoDB`等，排名不分先后，自己平时接触最多的也就是`MySQL`，`MongoDB`，好`MySQL`先来一份。

## 介绍
MySQL是一个开源数据库管理系统，通常作为流行的LEMP（Linux，Nginx，MySQL / MariaDB，PHP / Python / Perl）堆栈的一部分安装。它使用关系数据库和SQL（结构化查询语言）来管理其数据。

[CentOS 7](https://www.centos.org)更喜欢[MariaDB](https://mariadb.org)，它是由原始`MySQL`开发人员管理的`MySQL`分支，旨在替代MySQL。如果你在CentOS 7上运行`yum install mysql`，那么安装的是MariaDB，而不是MySQL。

<!-- more -->

## 清单
* OS: CentOS 7
* DataBase：MySQL 8.0.11

>`uname -a`查看你 Linux 系统的信息，按照系统版本选择对应的应用

```
[dc2-user@10-255-0-191 ~]$ uname -a
Linux 10-255-0-191 3.10.0-957.27.2.el7.x86_64 #1 SMP Mon Jul 29 17:46:05 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
# 这里：el7.x86_64 就表示，CentOS 7，32 位系统
```

## 安装

>官方下载 MySQL Community Server 地址：https://dev.mysql.com/downloads/mysql/
>清华镜像 MySQL 地址：https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/

```bash
# 1. 获取官方yum源安装包 mysql80-community-release-el7-1.noarch.rpm 是根据官网提供的版本信息
wget https://dev.mysql.com/get/`mysql80-community-release-el7-1.noarch.rpm`
# 例如清华镜像，这里的版本是 mysql-community-server-8.0.22-1.el7.x86_64.rpm
# wget https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-8.0-community-el7-x86_64/mysql-community-server-8.0.22-1.el7.x86_64.rpm
# 2. 安装rpm包
rpm -ivh mysql80-community-release-el7-1.noarch.rpm
# 3. 安装mysql-server
yum install -y mysql-server
# 4. 启动mysqld服务
systemctl start mysqld
# 5. 查看是否成功启动
ps aux|grep mysqld
# 6. 设置mysqld服务开机自启动
systemctl enable mysqld
```

## 配置

由于MySQL从5.7开始不允许在首次安装后，使用空密码进行登录，系统会随机生成一个密码以供管理员首次登录使用，这个密码记录在`/var/log/mysqld.log`文件中

```bash
# 1. 查看系统提供密码
cat /var/log/mysqld.log|grep 'A temporary password'
# 2. 使用获取到的密码登录MySQL
mysql -u root -p
# 3. 切换数据库
use mysql;
# 4. 修改root密码 your_password 替换成你自己的密码就可以了，这个密码是强密码，要求密码包含大小写字母、数字及标点符号，长度大于6
alter user 'root'@'localhost' identified by 'your_password';
```

## 链接
自己平时习惯使用 [Navicat](https://www.navicat.com.cn) 进行数据库操作，因此这里进行配置链接已在云端刚刚安装的MySQL服务
![linux-mysql](https://res.cloudinary.com/incoder/image/upload/v1532362215/blog/linux-mysql.png)
### ERROR 1130
按照上图图的配置信息链接MySQL，发现错误提示：`ERROR 1130: Host 'xxx.xxx.xxx.xxx' is not allowed to connect to this MySQL server`

#### 原因
不允许从远程登陆MySQL服务，只能在localhost

#### 解决方法
```bash
# 切换数据库
use mysql;
# 修改user 指定的host 为 %
update user set host = '%' where user = 'root';
# 成功修改
+-----------+------------------+
| host      | user             |
+-----------+------------------+
| %         | root             |
| localhost | mysql.infoschema |
| localhost | mysql.session    |
| localhost | mysql.sys        |
+-----------+------------------+
4 rows in set (0.00 sec)
```

### ERROR 2059
继续重试链接，错误提示：`ERROR 2059: Authentication plugin 'caching_sha2_password' cannot be loaded:The specified module could not be found.`

#### 原因
MySQL 8不支持动态修改密码验证方式

#### 解决方法
```bash
# 停止mysql
systemctl stop mysqld.service
# my.cnf文件中默认有下面的语句，删除前面的#号即可，没有的话就把它添加到my.cnf中 ，默认路径`/etc/my.cnf`
default-authentication-plugin=mysql_native_password
# 切换数据库
use mysql
# 给指定用户设置密码，这里`%`是因为之前已经将远程没有特殊指定，用%代替了localhost
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'your_password';
```

## 附录
* [How To Install MySQL on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-centos-7)
* [ERROR 1130](https://blog.csdn.net/nyist327/article/details/45074559)
* [ERROR 2059](https://blog.csdn.net/airt_xiang/article/details/80261674)