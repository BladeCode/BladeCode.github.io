---
title: Hexo Blog 迭代
date: 2018-05-02 18:18:18
categories: Hexo
tag: Build
---

最初博客通过[Cloudflare](https://www.cloudflare.com)反向代理进行HTTPS解析，放完五一假期，Github官方开始支持[自定义域名的HTTPS解析](https://blog.github.com/2018-05-01-github-pages-custom-domains-https)，在使用Cloudflare期间，经常性的521等问题烦恼，这次也可以名正言顺的弃用CloudFlare

**本次迭代内容**
* 弃用Cloudflare
* 自动化部署
* 常用设置
* 常用插件安装

<!-- more -->

## 弃用Cloudflare

1. 关闭Cloudflare中设置Page Rules
2. 删除Cloudflare的DNS记录
3. 还原域名配置中的DNS解析
4. 添加Github提供的IP解析

[官方自定义域名设置](https://help.github.com/articles/setting-up-an-apex-domain/#configuring-an-alias-or-aname-record-with-your-dns-provider)

## 自动化部署

>[Github Pages](https://pages.github.com)是Github 提供一个渲染静态的Web页面服务
* `{username}.github.io`仓库默认`master`分支
* 其他项目仓库，默认`gh-pages`分支
* [官方说明文档](https://help.github.com/articles/user-organization-and-project-pages)

因此`{username}.github.io`仓库，dev分支用来存储网站的源码，`master`分支存放生成的静态文件，这样一个仓库就可以管理整个项目。每次`push`新的功能，然而每次都需要先`push`到`dev`分支，然后生成静态文件，再`push`到`master`分支，这种重复性的操作，实在太不优雅，所以采用[Travis CI](https://travis-ci.org)进行自动化部署

接着Github支持自定义域名开启HTTPS的好消息，Travis CI (https://travis-ci.com) 也支持开源项目啦

> Travis CI 区别

* Travis-CI(https://travis-ci.org) ：GitHub公开项目
* Travis-CI(https://travis-ci.com) ：~~私有付费项目~~，[2018.05.02也开始支持开源项目](https://blog.travis-ci.com/2018-05-02-open-source-projects-on-travis-ci-com-with-github-apps?utm_source=Broadcast&utm_campaign=2may_release)

[GitHub Services are being deprecated](https://developer.github.com/changes/2018-04-25-github-services-deprecation)，因此本节的自动化部署就开启Travis CI (https://travis-ci.com) 集成方案

### 准备

1. 使用GitHub账号登录Travis-CI，并确认接受访问
2. 同步了GitHub存储库，转到您的配置文件页面并启用您想要构建的存储库  
3. 添加 `.travis.yml` 文件到构建部署项目的根目录下

### Hexo 自动部署

部署流程
![部署流程](https://res.cloudinary.com/incoder/image/upload/v1525517765/blog/gitpages-travis-ci-branch-deploy.png)

Hexo 部署脚本示例
```bash
# 设置语言
language: node_js
# 设置相应的版本
node_js: 
  - '12.16.3'
  # - lts/*
# 可以减少travis构建时间
cache:
    directories:
        - node_modules
before_install:
  # - npm config set bin-links false
  # - npm install -g hexo
  - npm install -g hexo-cli
# 安装hexo及插件
install:
  - npm install
before_script:
  - npm install -g mocha
  - git clone --branch master https://github.com/BladeCode/BladeCode.github.io.git public
script:
  # 清除
  - hexo cl
  # 生成
  - hexo g
after_script:
  - cd ./public
  - git init
  # 修改成自己的github用户名
  - git config user.name "BladeCode"
  # 修改成自己的GitHub邮箱
  - git config user.email "Jerry.x@outlook.com"
  - git add .
  - git commit -m "update by Travis-CI on `date '+%Y-%m-%d %H:%M:%S'`"
  # GH_token就是在travis中设置的token
  - git push --force --quiet "https://${GH_TOKEN}@${GH_REF}" master:master
branches:
  only:
  # 只监测这个分支，一有动静就开始构建
  - dev
env:
    global:
        # 设置仓库地址
        - GH_REF: github.com/BladeCode/BladeCode.github.io.git

```

## 常用设置

[NexT 配置使用手册](http://theme-next.iissnan.com)
[NexT 配置使用手册 {% label primary@新 %}](https://theme-next.js.org/docs/)

### NexT主题更新

[官方说明](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/DATA-FILES.md)

## 常用插件安装

* 文章字符统计 [hexo-symbols-count-time](https://github.com/theme-next/hexo-symbols-count-time)
* 修复LeanCloud访客计数器中的严重安全漏洞 [hexo-leancloud-counter-security](https://github.com/theme-next/hexo-leancloud-counter-security)
* 图片灯箱 [theme-next-fancybox3](https://github.com/theme-next/theme-next-fancybox3)
* 本地检索 [hexo-generator-searchdb](https://github.com/theme-next/hexo-generator-searchdb)
* 注脚 [hexo-renderer-markdown-it-plus](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus)
* 文章加密 [hexo-blog-encrypt](https://github.com/D0n9X1n/hexo-blog-encrypt)

## 其他

### 图床选择

* [个人网站中的静态文件云存储选择](https://jimmysong.io/posts/static-website-storage)
* [嗯，图片就交给它了](https://sspai.com/post/40499)
* [NexT主题无法备份解决方式](https://github.com/iissnan/hexo-theme-next/issues/932)
