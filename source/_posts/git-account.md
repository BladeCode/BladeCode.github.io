---
title: Git 多账号 
date: 2018-10-06 10:54:50
categories: Git
tag: [git account]
---

以前，git的账号只用来在Github上操作，随着积累Git管理的项目不仅仅只来自Github，还有一些其它Git项目托管的平台，例如：[Bitbucket](https://bitbucket.org)，[Coding](https://coding.net)，[Gitee](https://gitee.com)，[Gitlib](https://gitlab.com)，以及公司内Git仓库

不同的托管平台有着不同的Git账号，无法用一个账号来管理其它的仓库，而且由于不同的托管平台账号不同，因此需要添加不同账号的公钥，这样我们再能在对应平台用对应的账号进行操作

<!-- more -->

## 环境
* Windows 10 x64
* Git version 2.16.0

>这里Git的安装不在赘述

## 生成对应账号的密钥
```sh
# 进入到`your_pc_name/.ssh`,
cd .ssh
# Jerry.x@outlook.com 是我的Github的邮箱，这里需要替换成自己的邮箱
ssh-keygen -t rsa -C "Jerry.x@outlook.com"
# 命名文件名称或指定文件存放路径等
# 其它可以回车键进行确认，进行下一步
```
![git-account](https://res.cloudinary.com/incoder/image/upload/v1538887180/blog/git-account.png)

>完成后，将会生成`id_rsa_company.pub`（存放公钥）与`id_rsa_company`（存放私钥）两个文件

## 添加公钥到托管平台
* 在`.ssh`路径下，用文本编辑器打开`id_rsa_company.pub`文件，复制内容
* 在托管平台上添加ssh public key
以下以GitHub添加为例，其它平台类似
![git-add-key](https://res.cloudinary.com/incoder/image/upload/v1538887180/blog/git-add-key.png)

## 添加配置文件
在`.ssh`路径下，创建`config`文件，无文件后缀名，如下示例

```sh
# 配置github.com
Host github.com                 
    HostName github.com
    IdentityFile C:\\Users\\Jerry\\.ssh\\id_rsa
    PreferredAuthentications publickey
    User BladeCode

# 配置 company.domain.com
Host company.domain.com
    HostName company.domain.com
    IdentityFile C:\\Users\\Jerry\\.ssh\\id_rsa_company
    PreferredAuthentications publickey
    User Jerry xu
```

* `Host`：的名字可以取为自己喜欢的名字
* `HostName`：这个是真实的域名地址  
例如：https://<font color=red>github.com</font>/BladeCode/BladeCode.github.io.git，红色标注字段
* `IdentityFile`：这里是id_rsa的地址
* `PreferredAuthentications`：配置登录时用什么权限认证
可设为publickey，password publickey，keyboard-interactive等
* `User`：配置使用用户名

## 测试
```sh
ssh -T git@github.com
```
![git-test](https://res.cloudinary.com/incoder/image/upload/v1538887180/blog/git-test.png)

>git@<font color=red>github.com</font>，github.com就是上一步中`config`文件中配置的`HostName`字段内容