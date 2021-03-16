---
title: 微信小程序之 Vant实战（一）
date: 2021-02-12 11:11:11
categories: Wechat
tag: [Wechat, Vant]
---

过年正好时间比较集中，可以把之前的一个想法付诸实践，之前一直想给老爸做一个类似于账单管理的应用，方便他每天把客户需要物品记录成一个清单进行管理，其中主要包含已下功能点。其一，支持添加任务列表（账单）；其二，支持任务列表分享（账单）。以上是我的第一期规划功能规划，话不多说我们就一起来跟着我来完成这个小程序的开发吧，本篇主要讲小程序的初始化相关工作

<!-- more -->

这是我第一次来开发小程序，虽然之前有开发 Android 客户端的经验，有一定的客户端经验，但是小程序却一直没有去实践，我主要是觉得小程序的使用体验真的很差。但随着现在人们的硬件设备越来越好，并且微信团队在应用底层也做了很多的扩展和优化，现在使用小程序开发轻量级的应用还是很方便且高效

## 环境及选型

1. OS：macOS（11.2.1）
2. [IDE：WeChat Devtools（1.05.2102010）](https://developers.weixin.qq.com/miniprogram/dev/devtools/download.html)
3. Node：v15.5.0
4. [Vant：1.6.7](https://vant-contrib.gitee.io/vant-weapp/#/intro)
5. [微信小程序账号](https://mp.weixin.qq.com)

>关于账号的申请，这里不做讲解，请自行解决

## 初始化项目

### 创建项目

不废话，直接看图

![](https://res.cloudinary.com/incoder/image/upload/v1613191822/blog/wechat-create.png)

### 项目结构

项目是一个基于云开发的方式，创建完成后会包含云相关的一些操作实例

```text
bill
  ├── cloudfunctions/               # 云函数管理【清空当前文件夹下的内容】
  │    │── callback/
  │    │── echo/
  │    │── login/
  │    └── openapi/    
  ├── minprogram/
  │    │── components/              # 组件【清空当前文件夹下内容】
  │    │── images/                  # 图片管理【清空当前文件夹下内容】
  │    │── pages/                   # 页面管理【除 index 页面，其余都删除】
  │    │    │── addFunction/
  │    │    │── chooseLib/
  │    │    │── databaseGuide/
  │    │    │── deployFunctions/
  │    │    │── im/
  │    │    │── index/
  │    │    │    │── index.js
  │    │    │    │── index.json
  │    │    │    │── index.wxml
  │    │    │    │── index.wxss
  │    │    │    └── user-unlogin.png
  │    │    │── openapi/
  │    │    │── storageConsole/
  │    │    └── userConsole/
  │    │── style/                   # 样式管理
  │    │── app.js                   # 项目入口逻辑管理
  │    │── app.json                 # 组件库配置
  │    │── app.wxss                 # 全局样式设置
  │    └── sitemap.json             # 
  ├── project.config.json           # 项目配置文件
  └── README.md                     # 项目说明
```

### 精简项目

从上面我们可知初始化的项目，包含了一些示例，我们对其精简

1. 清空 cloudfunctions 目录下的云函数内容
2. 根据项目结构里的备注，进行删除相关的文件
    * 清空 components 文件夹下的组件
    * 清空 images 文件夹中的内容
    * 删除 pages 文件夹下，**除** index 的文件夹
    * 删除 index 文件夹下的 user-unlogin.png 文件
3. 修改文件内容
    * 清空 index 文件夹下 index.wxml，index.wxss 文件中的内容
    * 修改 index 文件夹下 index.js 文件内容
    * 清空 minprogram 文件夹下 app.wxss 文件中的样式内容
4. 修改配置
    * 移除 app.json 文件中 已经移除掉的 pages 的配置
    * 修改 project.config.json 文件，移除 miniprogram 的配置

### 添加 vant 组件

整个步骤，如下截图
![](https://res.cloudinary.com/incoder/image/upload/v1613403600/blog/wechat-init.png)

> 执行命令根据官方提供的方式和自身喜好选择，我这里使用的是 `yarn` 命令进行安装相关的依赖

### 测验效果

这里以添加 Button 为例来查看是否生效

1. 在 pages/index 路径下的 `index.json` 文件中，添加 vant 的 Button 组件
   ```json
    {
        "usingComponents": {
            "van-button": "@vant/weapp/button/index"
        }
    }
   ```
2. 在 pages/index 路径下的 `index.wxml` 文件中，添加 vant 的相关组件
    ```xml
    <van-button type="primary">主要按钮</van-button>
    <van-button type="info">信息按钮</van-button>
    <van-button type="warning">警告按钮</van-button>
    <van-button type="danger">危险按钮</van-button>
    ```
3. 编译，在模拟器中查看效果

>对于引用的组件，是公共的，可以写在 `app.json` 文件中

## 参考

1. [官方开发文档](https://developers.weixin.qq.com/miniprogram/dev/framework)
2. [微信小程序组件库Vant weapp的使用与weui零基础入门课程](https://www.bilibili.com/video/BV18V411C7VV)