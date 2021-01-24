---
title: MacBook Pro 疑难杂症
date: 2019-01-10 02:04:46
categories: macOS
tag: [Exp, iTerm2]
---

这是一篇记录使用macOS系统时遇到的一些疑难杂症

## AirPods 异常

在 AirPods 使用过程中，发现有时候耳机并不能正常工作。通常情况下，我会断开与 macOS 的连接，重新连接，如果还是不能正常工作，在 macOS 的系统蓝牙设置里面，移除连接的耳机设备，将耳机放入 AirPods 盒子里面，先盖上盒子，然后再打开盒子，此时并按住 AirPods 盒子背后的按钮，直到前面呼吸灯变成白色，然后再 macOS 的蓝牙里面找到新的设备，并连接配对。同时也可参考官方指引步骤 [连接并使用 AirPods 和 AirPods Pro](https://support.apple.com/zh-cn/HT207010)

### 单耳工作

换一个连接设备，检查耳机是否正常，如果是正常，那说明耳机没有问题，问题就出在 macOS 声音管理上面，打开`系统设置` -> `声音` -> `输出模式` ->`设置为居中的平衡模式（既双耳工作）`

![airpods-settings](https://res.cloudinary.com/incoder/image/upload/v1605689283/blog/airpods-single.png)