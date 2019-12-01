---
title: Gitlab 应用搭建
date: 2018-04-24 21:11:10
categories: Git
tag: Gitlab
---

我司团队之前一直使用SVN来进行代码托管，主要问题
1. 每次来个新人都需要找对应的[SVN](https://tortoisesvn.net/index.zh.html)管理员进行授权分配指定的仓库操作权限，有时候需要多个项目切换，还得再次提出进行仓库的指定
2. SVN都是以中文命名，这其实没啥，但是在[eclipse](https://eclipse.org) 以及[IDEA](https://www.jetbrains.com/idea/?fromMenu) ，[Xcode](https://developer.apple.com/xcode)等开发工具，链接地址都会把中文字进行编码，造成路径非常的长，强迫症的我这怎么忍得了
3. 产品相关的，设计相关的啥也都放在SVN里面，搞得SVN里面鱼龙混杂

<!-- more -->

因此在我提出及建议下，部门经理同意了对代码的管理进行隔离方便有效的对代码的授权监管，并同时制定代码的相关规范和服务的自动化部署等，提高团队的开发效率和代码质量。

本节主要介绍Gitlab的环境搭建和基础的功能配置

目的：
1. 搭建Gitlab服务
2. 和公司AD域账号关联，用域账号直接登录Gitlab
3. 挂载Gitlab 仓库到指定存储位置

## Gitlab安装

### 环境
* OS：CentOS 7
* Gitlab：[Gitlab CE 10.6.4](https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-10.6.4-ce.0.el7.x86_64.rpm)

>Gitlab 版本
* Gitlab Community Edition (CE)：社区版，免费，用户自行托管，通过社区提供技术支持
* Gitlab Enterprise Edition (EE)：企业版，付费，用户自行托管，提供附加的功能以及技术支持
* Gitlab.com：免费的SaaS服务，可以创建共有以及私有的版本库，可以购买额外的技术支持
* GitHost.io：由Gitlab提供的用户私有的独享服务

### Gitlab部署
1. 系统防火墙中打开HTTP和SSH访问
    ```bash
    sudo yum install -y curl policycoreutils-python openssh-server
    sudo systemctl enable sshd
    sudo systemctl start sshd

    sudo firewall-cmd --permanent --add-service=http
    sudo systemctl reload firewalld
    ```
2. 安装Postfix发送通知邮件。如果您想使用其他解决方案发送电子邮件，请跳过此步骤并在安装GitLab后配置外部SMTP服务器
    ```bash
    sudo yum install postfix
    sudo systemctl enable postfix
    sudo systemctl start postfix
    ```
3. 添加GitLab软件包存储库
    ```bash
    curl -LJO https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-10.0.0-ce.0.el7.x86_64.rpm
    ```
4. 安装软件包
    ```bash
    rpm -i gitlab-ce-10.0.0-ce.0.el7.x86_64.rpm
    ```

    完成安装如下日志显示：

    ```
           *.                  *.
          ***                 ***
         *****               *****
        .******             *******
        ********            ********
       ,,,,,,,,,***********,,,,,,,,,
      ,,,,,,,,,,,*********,,,,,,,,,,,
      .,,,,,,,,,,,*******,,,,,,,,,,,,
          ,,,,,,,,,*****,,,,,,,,,.
             ,,,,,,,****,,,,,,
                .,,,***,,,,
                    ,*,.
    


         _______ __  __          __
        / ____(_) /_/ /   ____ _/ /_
       / / __/ / __/ /   / __ \`/ __ \
      / /_/ / / /_/ /___/ /_/ / /_/ /
      \____/_/\__/_____/\__,_/_.___/

    ```
5. 编译配置文件
    ```bash
    cd /opt/gitlab/bin
    ./gitlab-ctr reconfigure
    ```
6. 启动服务
    ```bash
    ./gitlab-ctl start
    ```
    >
    * 成功启动服务，默认路径访问：http://localhost:80
    * 默认安装位置 `/opt/gitlab/`
    * 配置文件默认路径 `/etc/gitlab/gitlab.rb`
    * 默认账号：root，密码：5iveL!fe

## 常用配置项修改
以下配置项的修改，完成后**均需要重新编译**文件（配置文件默认路径 `/etc/gitlab/gitlab.rb`），默认，**并重启Gitlab**服务

### 访问地址
修改`external_url`为Gitlab对应机器IP所配置的域名
![gitlab-url](https://res.cloudinary.com/incoder/image/upload/v1525517587/blog/gitpages-gitlab-url.png)

### LDAP启用
修改`host`,`port`,`bind_dn`,`password`,`base`参数即可
![gitlab-ladp](https://res.cloudinary.com/incoder/image/upload/v1525517612/blog/gitpages-gitlab-ldap.png)

各参数解释：
* `host` 和 `port` 是 LDAP 服务的主机地址及端口
* `bind_d`n 和 `password` 是一个管理 LDAP 的 dn 及密码
* `base` 表示 LDAP 将以该 dn 为 节点，向下查找用户
* `user_filter` 表示以某种过滤条件筛选用户
* `attributes` 表示 GitLab 中的字段与 LDAP 中哪些字段可以相互对应，比如可以用 LDAP 中的 uid 来作为 GitLab 用户名

编译重启后，查看登录是否已经显示LDAP登录入口

![gitlab-ldap-login](https://res.cloudinary.com/incoder/image/upload/v1525517639/blog/gitpages-gitlab-ldap-login.png)

为了安全我们需要关闭 GitLab 自己的注册功能，这样新用户只能通过 LDAP 认证的方式进行登陆。

![gitlab-sign-up](https://res.cloudinary.com/incoder/image/upload/v1525517671/blog/gitpages-gitlab-sign-up.png)

### 存储仓库修改
默认仓库存储位置：`/var/opt/gitlab/git-data/repositories/`
![gitlab-dirs](https://res.cloudinary.com/incoder/image/upload/v1525517697/blog/gitpages-gitlab-dirs.png)

### Gitlab日志
默认日志位置： `/var/log/gitlab`

```bash
cd /opt/gitlab/bin
gitlab-ctl tail -f nginx/gitlab_access.log
```
或者在Gitlab服务的系统设置中查看
![gitlab-logs](https://res.cloudinary.com/incoder/image/upload/v1525517725/blog/gitpages-gitlab-logs.png)

## 附录
* [官方安装教程](https://about.gitlab.com/installation)
* [官方配置文件](https://docs.gitlab.com.cn/omnibus/settings/README.html)