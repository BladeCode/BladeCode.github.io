---
title: Hugo 初体验 
date: 2018-07-11 16:34:10
categories: Hugo
tag: Build
---

个人博客使用 [Hexo](https://hexo.io/zh-cn/index.html) 搭建，使用效果很不错，[RootCluster](https://github.com/RootCluster) 组织主要存放自己新技术的学习和一些Demo实验。该组织同样也可以使用Github pages服务，因此也需要给RootCluster构建一个静态页面，可用直观清晰的看自己的项目，虽然之前已使用Hexo构建，为了了解其他的静态页面构建，所以这次选择了 [Hugo](https://gohugo.io)

[Hugo](https://gohugo.io) 是世界上最快的静态网站引擎。它是用 [Go](https://golang.org)（aka Golang）编写的，由 [bep](https://github.com/bep)，[spf13](https://github.com/spf13) 和[朋友](https://github.com/gohugoio/hugo/graphs/contributors)开发

<!-- more -->

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
    cd project_name && hugo serve
    ```
    ![hugo_init](https://res.cloudinary.com/incoder/image/upload/v1531314737/blog/hugo_init.png)

* [主题安装](https://themes.gohugo.io)

    这里选择[Elate](https://themes.gohugo.io/hugo-elate-theme)主题作为组织的网站

    ![](https://res.cloudinary.com/incoder/image/upload/v1531316293/blog/hugo_theme.png)

## GitHub Action 部署

1.  新生成部署 key
  ```shell
  # 1. 进入本地电脑的 .ssh 文件夹 
  cd .ssh/
  # 2. 生成部署 key
  ssh-keygen -t rsa -b 4096 -C "Jerry.x@outlook.com" -f id_rsa_deploy -N ""
  ```
2. 添加部署 key 到项目仓库设置中

```yaml
# This is a basic workflow to help you get started with Actions

name: CI

# Controls when the action will run. 
on:
  # Triggers the workflow on push or pull request events but only for the master branch
  push:
    branches: master
  pull_request:
    branches: dev

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2
        with:
          submodules: true
          fetch-depth: 0
      # Runs a single command using the runners shell
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2.3.1
        with:
          hugo-version: '0.61.0'
      # Runs a set of commands using the runners shell
      - name: Build
        run: hugo --minify
      
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          publish_dir: ./public
          commit_message: ${{ github.event.head_commit.message }}
```

### Travis

```yml
language: go

go:
  - master  # 使用最新版本

# Specify which branches to build using a safelist
# 分支白名单限制: 只有hugo分支的提交才会触发构建
branches:
  only:
    - dev 

install:
# 安装最新的hugo
  - go get -v github.com/gohugoio/hugo

script:
# 运行hugo命令
  - hugo

deploy:
  provider: pages # 重要，指定这是一份github pages的部署配置
  skip-cleanup: true # 重要，不能省略
  local-dir: public # 静态站点文件所在目录
  target-branch: master # 要将静态站点文件发布到哪个分支
  github-token: $GITHUB_TOKEN # 重要，$GITHUB_TOKEN是变量，需要在GitHub上申请、再到配置到Travis
  keep-history: true # 是否保持target-branch分支的提交记录
  on:
    branch: dev # 博客源码的分支
```

## 参考

1. [Hugo Documentation](https://gohugo.io/documentation/)
2. [Hugo setup](https://github.com/marketplace/actions/hugo-setup)