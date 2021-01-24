---
title: MacBook Pro 疑难杂症
date: 2020-11-13 02:04:46
categories: macOS
tag: [Exp, iTerm2]
---

这是一篇记录使用macOS系统时遇到的一些疑难杂症

## macOS Big Sur

在 2020.11.13 正式推送了 macOS Big Sur version 11.0.1 版本，这一个版本是改动比较大的版本，这里关于它的新特性就不做介绍了，有兴趣的请查看官方网站介绍 [Big Sur](https://www.apple.com.cn/macos/big-sur)

![](https://res.cloudinary.com/incoder/image/upload/v1605885064/blog/macOS_Big_Sur.png)

<!-- more -->

### Glance 失效

Glance 是一个快速预览增强，可以对一些文件进行快速预览，大大提高我们的日常效率，但该应用在 Big Sur 版本中不兼容，由于作者已入职 Apple，且对项目做了归档，不在维护，因此该问题依旧没有解决，可以使用一个付费的应用[iPreView](https://apps.apple.com/cn/app/ipreview-powerful-quick-look/id1519213509?l=en&mt=12)来满足当前需要

>[Glance 在 Big Sur 系统中失效](https://v2ex.com/t/725909)

## AirPods 异常

在 AirPods 使用过程中，发现有时候耳机并不能正常工作。通常情况下，我会断开与 macOS 的连接，重新连接，如果还是不能正常工作，在 macOS 的系统蓝牙设置里面，移除连接的耳机设备，将耳机放入 AirPods 盒子里面，先盖上盒子，然后再打开盒子，此时并按住 AirPods 盒子背后的按钮，直到前面呼吸灯变成白色，然后再 macOS 的蓝牙里面找到新的设备，并连接配对。同时也可参考官方指引步骤 [连接并使用 AirPods 和 AirPods Pro](https://support.apple.com/zh-cn/HT207010)

### 单耳工作

换一个连接设备，检查耳机是否正常，如果是正常，那说明耳机没有问题，问题就出在 macOS 声音管理上面，打开`系统设置` -> `声音` -> `输出模式` ->`设置为居中的平衡模式（既双耳工作）`

![airpods-settings](https://res.cloudinary.com/incoder/image/upload/v1605689283/blog/airpods-single.png)