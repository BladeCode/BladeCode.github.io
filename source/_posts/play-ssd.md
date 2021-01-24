---
title: 搞定 m.2 接口 SSD 
date:  2020-12-10 09:44:46
categories: Play
tag: [SSD]
---

![](https://res.cloudinary.com/incoder/image/upload/v1611489458/blog/dell-ssd.jpg)

<!-- more -->

公司原装配置电脑磁盘性能太差，实在是不能满足我的日常骚操作，然后就自己买了一个 m.2 接口的 SSD 硬盘，毕竟电脑之前已经有系统了，而且也已经安装好了开发环境，如果现在在新的 SSD 上直接安装新的系统，那么需要将之前的开发环境再折腾一遍，实在是伤不起。那么有没有别的方式。你别说哦，还真的有，方法是用一些工具对现有系统进行 clone 到新的 SSD 磁盘上。这都很好办，比如：[傲梅分区助手](https://www.disktool.cn)，[DiskGenius](https://www.diskgenius.cn) 都有系统迁移功能，可参考文章下方的参考地址，内有视频教程

>注意：要设置好设置默认系统启动引导为新的磁盘

## 问题

一开始，我觉得这么简单的操作能有什么问题，迁移完系统，并设置好系统引导，然而我发现并不能按照预期使用 SSD 来启动，试了好几遍，调整了 BIOS 的启动选项，依旧不能解决。后来我将原系统的磁盘拆下来，只留 SSD 磁盘，开机就能按照预期启动了，正常后在把原系统磁盘再装回去，同时记得检查下系统引导，确保还依旧是使用 SSD 系统盘

## 参考

1. [SSD系统迁移工具：轻松迁移系统到SSD](https://www.disktool.cn/jiaocheng/migrate-system.html)
2. [使用分区助手快速将Windows系统迁移到新磁盘](https://www.disktool.cn/jiaocheng-new/2019/how-to-migrate-system.htmlv)