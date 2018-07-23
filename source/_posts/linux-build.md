---
title: Linux 常用应用安装
date: 2018-05-15 00:32:10
categories: Linux
tag: Build
---

作为Android开发者，目标主要是在客户端，平时也就是和服务端对接数据接口，很少直接干到服务端的Linux机器，随着这波推动团队技术平台基础开发工具模块的完善，拿到了一台Linux机器，重新构建移动端的测试服务器。

该机器主要功能：
1. 提供移动端服务Api接口
2. 提供移动端通讯录管理授权服务
3. 提供企业微信通讯录同步服务
4. 管理移动端服务器Api接口文档

也是第一次正式的从头开始安装所需软件及应用部署，虽然这些工作可以完全找运维去处理，难得这样的机会从头开始去熟悉Linux。

**安卓，是一个基于Linux内核的开放源代码移动操作系统**，因此多了解Linux是一件双赢的事情，基于当前机器需要提供的服务，安装部署需要的软件应用

<blockquote class="blockquote-center">废话不多说，上来就是干</blockquote>

查看当前系统版本信息
```bash
lsb_release -a
```
以下软件版本，请下载对应支持系统的软件

## Java

[官方下载地址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)，选择需要的版本下载安装包
>官方提供了`.rpm`,`.gz`两种格式安装包

```bash
# 1.下载安装包
#    拷贝安装包到需要安装的服务器
# 2.解压并安装
#    `.rpm`格式安装(jdk-xxx.rpm更换成对应的文件名)
sudo rpm -ivh jdk-xxx.rpm
#    `.gz`格式安装(解压到指定目录，常存放`/usr/java/`路径)
tar zxvf jdk-xxx.tar.gz -C /usr/java/
# 3.设置环境变量
vim /etc/profile
# JAVA_HOME=/usr/java/jdk1.8.0_172 (修改这里路径，指向jdk安装路径)
# PATH=$JAVA_HOME/bin:$PATH
# CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
# export JAVA_HOME PATH CLASSPATH
# 4.编译配置文件，使修改生效
source /etc/profile
# 5.验证jdk是否安装成功
java –version
```

## Tomcat

[官方下载地址](http://tomcat.apache.org)，选择需要的版本下载安装包
>官方提供了`.zip`,`.gz`两种格式安装包，Linux服务器下载`Core`类即可

```bash
# 1.下载安装文件
wget http://mirrors.hust.edu.cn/apache/tomcat/tomcat-9/v9.0.8/bin/apache-tomcat-9.0.8.tar.gz
# 2.解压安装文件(解压到指定目录，常存放`/usr/tomcat/`路径)
tar -zxvf apache-tomcat-9.0.8.tar.gz -C /usr/tomcat/
# 3.启动tomcat
cd /usr/local/tomcat/bin
./startup.sh
# 4.关闭tomcat
./shutdown.sh
```

### 配置Web管理账号
* 修改文件conf/tomcat-users.xml，在元素中添加帐号密码，需要指定角色
```bash
vim /usr/local/tomcat/conf/tomcat-users.xml
#   <tomcat-users>
#       <user name="admin" password="admin" roles="admin-gui,manager-gui" />
#   </tomcat-users>
```

### 配置端口
* 可以修改conf目录下的文件server.xml，修改Connector元素(Tomcat的默认端口是8080)，需要重新启动Tomcat服务生效
```bash
vim /usr/local/tomcat/conf/server.xml
# <Connector port="9999" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" /> 
```

### 应用部署
* 放置需部署包到容器中`webapps`路径
```bash
cd /usr/local/tomcat/webapps
```
* 启动服务
```bash
cd /usr/local/tomcat/bin
./startup.sh
```

## Apache
一般系统中以及包含apache应用  
[官方下载地址](http://httpd.apache.org/download.cgi)，选择需要的版本下载安装包
>官方提供了`.bz2`,`.gz`两种格式安装包

### 安装
查看系统中是否已包含httpd应用
```
rpm -qa | grep httpd 或 yum list | grep httpd
```

* 方式一
```bash
# 1.下载需要的版本文件
wget http://apache.claz.org//httpd/httpd-2.4.33.tar.gz
# 2.解压安装文件(解压到指定目录，常存放`/usr/local/httpd/`路径)
tar -zxvf httpd-2.4.33.tar.gz -C /usr/local/httpd/
```

* 方式二(推荐)
```bash
# 1.下载安装httpd
yum install httpd
```

### 卸载
```bash
yum erase httpd.x86_64 或 rpm -e httpd.x86_64
```

### 常用命令
```bash
# 查看服务运行状态
systemctl status httpd.service
# 启动apache服务
systemctl start httpd.service
# 停止apache服务
systemctl stop httpd.service
```

RPM默认安装路径：

|    路径    | 说明 |
| ---------- | --- |
| /etc           |  一些设置文件放置的目录如/etc/crontab |
| /usr/bin       |  一些可执行文件 |
| /usr/lib       |  一些程序使用的动态函数库 |
| /usr/share/doc |  一些基本的软件使用手册与帮助文档 |
| /usr/share/man |  一些man page文件 |

## Nginx
[官方下载地址](http://nginx.org/download)，选择需要的版本下载安装包(最新安装版本1.14.0)
>官方提供了`.zip`,`.gz`两种格式安装包

### 安装

* 方式一
```bash
# 1.下载安装文件
wget http://nginx.org/download/nginx-1.14.0.tar.gz
# 2.解压安装文件(解压到指定目录，常存放`/usr/local/`路径)
tar -zxvf nginx-1.14.0.tar.gz -C /usr/local/
# 3. 编译安装依赖库
cd /usr/local/nginx/
./configure
```

* 方式二
```bash
# 默认安装路径/etc/nginx/
yum install nginx
```

### 常用命令
* 加压文件安装常用命令
```bash
# 停止ngix
/usr/local/nginx/sbin/nginx -s quit
# 重新载入nginx(当配置信息发生修改时)       
/usr/local/nginx/sbin/nginx -s reload
# 查看版本     
/usr/local/nginx/sbin/nginx -v
# 查看nginx的配置文件的目录            
/usr/local/nginx/sbin/nginx -t
# 查看帮助信息            
/usr/local/nginx/sbin/nginx -h            
```
* yum安装常用命令
```bash
# 启动
systemctl start nginx
# 停止 
systemctl stop nginx
# 重启 
systemctl restart nginx
# 查看运行状态 
systemctl status nginx
# 开机启动 
systemctl enable nginx 
```

## Node
一般系统中以及包含apache应用  
[官方下载地址](https://nodejs.org)，选择需要的版本下载安装包
>官方提供了`.gz`,`.7z`,`zip`等多种格式安装包

### 安装
```bash
# 1.下载安装文件
wget https://nodejs.org/download/chakracore-release/v8.6.0/node-v8.6.0-linux-x64.tar.gz
# 2.解压安装文件(解压到当前目录)
tar -zxf node-v8.6.0-linux-x64.tar.gz
# 3.建立软链接，实现全局访问
ln -s /root/node-v8.6.0-linux-x64/bin/node /usr/local/bin/node
ln -s /root/node-v8.6.0-linux-x64/bin/npm /usr/local/bin/npm
```

## 常用命令

### 文件查找
#### find
find命令是根据文件的属性进行查找，如文件名，文件大小，所有者，所属组，是否为空，访问时间，修改时间等。 
* 基本格式：
find  path expression
* 示例：
    * 在根目录下查找文件httpd.conf，表示在整个硬盘查找
    find / -name httpd.conf
    * 表示当前目录下查找文件名开头是字符串‘srm’的文件
    find . -name 'srm*'
    * 查找在系统中最后10分钟访问的文件(access time)
    find / -amin -10
    * 查找在系统中属于fred这个用户的文件
    find / -user fred
    * 查找出小于1000KB的文件
    find / -size -1000k

#### grep
grep是根据文件的内容进行查找，会对文件的每一行按照给定的模式(patter)进行匹配查找。
* 基本格式：
find expression
* 主要参数：
    －c：只输出匹配行的计数。
    －i：不区分大小写
    －h：查询多文件时不显示文件名。
    －l：查询多文件时只输出包含匹配字符的文件名。
    －n：显示匹配行及行号。
    －s：不显示不存在或无匹配文本的错误信息。
    －v：显示不包含匹配文本的所有行。
* 示例：
    * 显示所有包含每行字符串至少有5个连续小写字符的字符串的行
    grep ‘[a-z]\{5\}’ aa
    * 显示所有以d开头的文件中包含 test的行
    grep 'test' d*

### 进程相关
* 查看指定服务进程
```bash
# 查看httpd服务进程
ps -ef | grep httpd
# UID        PID  PPID  C STIME TTY          TIME CMD
# root      7192  7103  0 19:59 pts/3    00:00:00 grep --color=auto httpd
```
    {% note info %} 
    * UID：用户ID 
    * PID：进程ID 
    * PPID：父进程ID 
    * C：CPU用于计算执行优先级的因子。数值越大，表明进程是CPU密集型运算，执行优先级会降低；数值越小，表明进程是I/O密集型运算，执行优先级会提高 
    * STIME：进程启动的时间 
    * TTY：完整的终端名称 
    * TIME：CPU时间 
    * CMD：完整的启动进程所用的命令和参数
    {% endnote %}

* 杀死指定进程
```bash
kill -9 pid（逐个都删除）
```
>