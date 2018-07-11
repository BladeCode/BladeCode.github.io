---
title: Hugo 初体验 
date: 2018-07-11 16:34:10
categories: Hugo
tag: Build
---

个人博客使用[Hexo](https://hexo.io/zh-cn/index.html)搭建，使用效果很不错，在Github上创建组织[RootCluster](https://github.com/RootCluster)，该组织主要存放自己新技术的学习和一些Demo实验。组织同样也可以使用Github pages服务，因此给RootCluster构建一个静态页面，可用直观清晰的看自己的项目，加之之前已用Hexo，所以这次的静态页面构建选择了新的一个静态页面生成工具，[Hugo](https://gohugo.io)是一款使用Go语言开发的静态页面生成框架。

[Hugo](https://gohugo.io)是世界上最快的静态网站引擎。它是用[Go](https://golang.org)（aka Golang）编写的，由[bep](https://github.com/bep)，[spf13](https://github.com/spf13)和[朋友](https://github.com/gohugoio/hugo/graphs/contributors)开发

## 材料准备
* SystemOS：Windows 10
* [Chocolatey](https://chocolatey.org)：Windows的包管理器
* [Hugo](https://gohugo.io/getting-started/installing)

## 安装
### Chocolatey安装
如果已安装，跳过该步骤  
* 使用 PowerShell.exe
```bash
Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```
* 使用 cmd.exe
```bash
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```
以上两种方式，选择其一即可
![PowerShell.exe 演示](https://res.cloudinary.com/incoder/image/upload/v1531314279/blog/hugo_install.png)

### hugo安装

```bash
choco install hugo -confirm
```

### 初始化Hugo
* 初始化hugo模板
```bash
hugo new site project_name
```

* 进入项目并启动项目
```bash
cd project_name 
hugo serve
```

![hugo_init](https://res.cloudinary.com/incoder/image/upload/v1531314737/blog/hugo_init.png)

* [主题安装](https://themes.gohugo.io)

这里选择[Elate](https://themes.gohugo.io/hugo-elate-theme)主题作为组织的网站

![主题安装](https://res.cloudinary.com/incoder/image/upload/v1531316293/blog/hugo_theme.png)