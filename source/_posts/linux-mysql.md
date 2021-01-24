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

[CentOS 7](https://www.centos.org)更喜欢[MariaDB](https://mariadb.org)，它是由原始`MySQL`开发人员管理的`MySQL`分支，旨在替代MySQL。<font color=red size=4>如果你在CentOS 7上运行 `yum install mysql`，那么安装的是MariaDB，而不是MySQL</font>

<!-- more -->

对于 MySQL 也是有好几个类别
1. MySQL Community Server：社区版本，开源免费，但不提供官方技术支持
2. MySQL Enterprise Edition：企业版本，需付费，可以试用 30 天
3. MySQL Cluster 集群版：开源免费。可将几个 MySQL Server 封装成一个 Server
4. MySQL Cluster CGE：高级集群版，需付费
5. MySQL Workbench：一款专为 MySQL 设计的 ER/数据库建模工具，分为两个版本
    * MySQL Workbench OSS：社区版
    * MySQL WorkbenchSE：商用版

先检查服务器是否已经安装了mariadb
```bash
# 检查是否安装了 mariadb 客户端
rpm -qa | grep mariadb
# 检查是否安装了 mariadb 服务端
rpm -qa | grep mariadb-server
```
执行结果如下图，如果没有任何提示，则表示没有安装
![](https://res.cloudinary.com/incoder/image/upload/v1606229882/blog/mariadb-check.png)

执行检查命令，发现有 mariadb 服务，则需要先卸载 `mariadb` 相关服务
```bash
# mariadb 相关卸载
rpm -qa |grep mariadb |xargs yum remove -y
```

![](https://res.cloudinary.com/incoder/image/upload/v1606235520/blog/mariadb-uninstall.png)

## 清单

* OS: CentOS 7
* DataBase：MySQL 8.0.11

>`uname -a`查看你 Linux 系统的信息，按照系统版本选择对应的应用

```
[dc2-user@10-255-0-191 ~]$ uname -a
Linux 10-255-0-191 3.10.0-957.27.2.el7.x86_64 #1 SMP Mon Jul 29 17:46:05 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
# 这里，el7.x86_64 分别表示，el7：CentOS 7，x86_64：64 位系统
```

## 安装

>官方下载 MySQL Community Server 地址：https://dev.mysql.com/downloads/mysql/
>清华镜像 MySQL 地址：https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/

![](https://res.cloudinary.com/incoder/image/upload/v1606147634/blog/mysql-download.png)

### 在线安装（推荐方式）

适用于当前安装服务器可以正常互联网访问，如上图，选择方式进入[MySQL Yum Repository](https://dev.mysql.com/downloads/repo/yum)，选择对应系统的版本，比如 `mysql80-community-release-el7-3.noarch.rpm`

![](https://res.cloudinary.com/incoder/image/upload/v1606227057/blog/mysql-line-rpm.png)

根据自己设备网络情况选择使用的下载地址
* 官方地址：https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
* 清华镜像：https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-8.0-community-el7-x86_64/mysql80-community-release-el7-3.noarch.rpm

```bash
# 1. 获取官方yum源安装包 mysql80-community-release-el7-3.noarch.rpm 是根据官网提供的版本信息
wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
# 2. 安装rpm包
rpm -ivh mysql80-community-release-el7-3.noarch.rpm
# 3. 安装mysql-server
yum install -y mysql-server
# 如果你需要设置不区分大小写，必须在第一次启动 mysqld 服务前设置，lower_case_table_names=1
# vim /etc/my.cnf
# 4. 启动mysqld服务
systemctl start mysqld
# 5. 查看是否成功启动
ps aux|grep mysqld
# 6. 设置mysqld服务开机自启动
systemctl enable mysqld
```

>设置不区分大小写参数，文件地址 `/etc/my.cnf`

### 离线安装

适用于当前安装服务器无法互联网访问，如安装截图所示，选择 [MySQL Community Server](https://dev.mysql.com/downloads/mysql/) 类别，然后选择对应系统及版本进行下载，总共需要如下所示相关的应用安装包

按照<font color=red size=4>此顺序进行安装</font>

```shell
rpm -ivh mysql-community-common-8.0.22-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-plugins-8.0.22-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-8.0.22-1.el7.x86_64.rpm
rpm -ivh mysql-community-libs-compat-8.0.22-1.el7.x86_64.rpm
rpm -ivh mysql-community-embedded-compat-8.0.22-1.el7.x86_64.rpm
rpm -ivh mysql-community-devel-8.0.22-1.el7.x86_64.rpm
rpm -ivh mysql-community-client-8.0.22-1.el7.x86_64.rpm
rpm -ivh mysql-community-server-8.0.22-1.el7.x86_64.rpm
```
安装 `mysql-community-devel-8.0.22-1.el7.x86_64.rpm` 时出错，需要先执行 `yum install openssl-devel` 命令进行安装 openssl-devel，如下图
![](https://res.cloudinary.com/incoder/image/upload/v1606415382/blog/mysql-community-devel.png)

安装完上面的包，进行启动 MySQL 服务，并进行相应的配置
```bash
# 如果你需要设置不区分大小写，必须在第一次启动 mysqld 服务前设置，lower_case_table_names=1
# vim /etc/my.cnf
# 启动mysqld服务
systemctl start mysqld
# 查看是否成功启动
ps aux|grep mysqld
# 设置mysqld服务开机自启动
systemctl enable mysqld
```

整个安装设置过程如下图
![](https://res.cloudinary.com/incoder/image/upload/v1607229288/blog/mysql-offline-install.png)

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
# 5. 刷新修改
flush privileges;
```

## 默认信息

### 配置信息地址

默认安装配置信息：<font color=red size=4>/etc/my.cnf</font>

### MySQL 安装路径

```bash
# 查看 MySQL 安装的位置
which mysqld
```

### 其他配置信息

![](https://res.cloudinary.com/incoder/image/upload/v1607229288/blog/mysql-cnf.png)

## 连接

自己平时习惯使用 [Navicat](https://www.navicat.com.cn) 进行数据库操作，因此这里进行配置链接已在云端刚刚安装的MySQL服务
![linux-mysql](https://res.cloudinary.com/incoder/image/upload/v1532362215/blog/linux-mysql.png)

### ERROR 2003

通过本地的工具无法连接到服务器上的 MySQL 服务，错误提示：`ERROR 2003 (HY000): Can't connect to MySQL server on '116.85.58.6' (60)`

进行原因排查，分别通过 `ping`，`telnet` 命令来检查网络连接情况，发现 IP 是通的，端口不同，那么去看服务器是不是开启了防火墙，如果开启了防火墙，需要将 MySQL 服务的端口排除

{% note warning %} 
对于开启了防火墙的，可以将防火墙关闭，或者设置端口白名单
{% endnote %}
```bash
# 查看防火墙状态，结果显示为running或not running
## 如果显示，-bash: firewall-cmd: command not found，则表示当前服务器没有安装防火墙
firewall-cmd --state
# 关闭防火墙firewall
systemctl stop firewalld.service
systemctl disable firewalld.service
# 关闭防火墙firewall后开启
systemctl start firewalld.service
# 开启端口
## zone -- 作用域
## add-port=80/tcp -- 添加端口，格式为：端口/通讯协议
## permanent -- 永久生效，没有此参数重启后失效
firewall-cmd --zone=public --add-port=3306/tcp --permanent
## 开启3306端口后，workbench或naivcat 就能连接到MySQL数据库了
```

![](https://res.cloudinary.com/incoder/image/upload/v1606417200/blog/mysql-link.png)

SSH 连接 MySQL 所在的服务器，查看防火墙情况，但发现压根没有防火墙，那么此时，要去看看你的云服务器的安全组设置规则，将 3306 暴露出来，查看云服务器安全组设置
![](https://res.cloudinary.com/incoder/image/upload/v1606418056/blog/didi-rule.png)

在已有安全组中添加安全规则或者新增安全组在新的安全组中添加安全规则
![](https://res.cloudinary.com/incoder/image/upload/v1606418056/blog/didi-add-rule.png)

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
# 查看 user 表中的信息
select host, user from user;
# 成功修改s
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

##### 方法一

修改plugin默认的 `caching_sha2_password`  为 `mysql_native_password`

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

##### 方法二

更新你的数据库连接工具，Navicat version12+，Workbench version8+

### BDB1507

BDB1507 Thread died in Berkeley DB library，由于其他操作原因造成数据库被破坏问题，可以通过如下命令进行数据库修复

```bash
cd /var/lib/rpm/
for i in `ls | grep 'db.'`;do mv $i $i.bak;done
rpm --rebuilddb
yum clean all
```

![](https://res.cloudinary.com/incoder/image/upload/v1607750118/blog/rpm-rebuild.png)

## 卸载

### 应用卸载

```shell
# 快速删除
yum remove mysql mysql-server mysql-libs mysql-server
# 查找残留文件（例如：mysql-community-server-8.0.22-1.el7.x86_64）
rpm -qa | grep -i mysql
# 将查询出来的应用删除
yum remove mysql-community-server-8.0.22-1.el7.x86_64
# 查找残余目录
## /etc/selinux/targeted/active/modules/100/mysql
## /etc/selinux/targeted/tmp/modules/100/mysql
## /var/lib/mysql
## /var/lib/mysql/mysql
## /usr/share/mysql
find / -name mysql
# 删除残余目录
rm -rf /etc/selinux/targeted/active/modules/100/mysql
rm -rf /etc/selinux/targeted/tmp/modules/100/mysql
rm -rf /var/lib/mysql
rm -rf /var/lib/mysql/mysql
rm -rf /usr/share/mysql
# 再次检查残余目录，确保没有残余
find / -name mysql
```

### 开机自启（可选）

当你安装时设置了开机启动，当不需要再开机启动时，那么再卸载完数据库后，记得在开启启动的列表中删除数据库

```bash
chkconfig --list | grep -i mysql
chkconfig --del mysqld
```

## 附录

* [How To Install MySQL on CentOS 7](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-centos-7)
* [ERROR 1130](https://blog.csdn.net/nyist327/article/details/45074559)
* [ERROR 2059](https://blog.csdn.net/airt_xiang/article/details/80261674)