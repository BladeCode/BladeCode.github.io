---
title: 开源协议，该如何选择
date: 2020-11-25 22:30:10
categories: Open Source
tag: License
---

现如今软件行业的发展完全离不开开源社区，很多优秀的软件应用、技术都能看到开源软件的影子，我们都是站在巨人的肩膀上。对于软件行业的从业者，能为开源项目贡献自己的力量，或是将自己对某一个细分领域所做的研究实践开源出来，这是一件非常值得骄傲的事情。而要参与一个大型的开源项目，你除了需要该项目涉及的核心技术知识外，还需要了解一定的开源项目运转方式等，对于如何参与开源项目，这里暂不做过多的介绍，有兴趣的可以移步 Gitee 发起的《[开源指北](https://gitee.com/gitee-community/opensource-guide)》项目，该项目中详细介绍了如何参与开源项目。本篇文章也不啰嗦这一点，仅仅围绕开源协议，我们应该清楚的常识和注意的点

<!-- more -->

在软件开发中**通常**有两种情况我们需要考虑软件的开源协议或者使用协议
1. 我们需要使用到业界的一些优秀的软件包来提高我们开发的效率，避免了重复早轮子，所选择的这些软件包我们不但考虑功能的同时，也要考虑软件包的授权协议
2. 我们需要将自己的经验或者软件产品需要开源时，为了保护自己的权益，我们也需要选择一个合适的开源协议

我这里还是引用比较经典 [阮一峰](http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html) 文章中所绘制关于如何选择开源协议的图
![](http://www.ruanyifeng.com/blogimg/asset/201105/free_software_licenses.png)
图中已经很清楚的表示了如何去选择 [LGPL](https://baike.baidu.com/item/LGPL), [Mozilla](https://www.mozilla.org/en-US/MPL/), [GPL](), [BSD](), [MIT](), [Apache]() 这 6 种协议

## 常用协议

这里我们通过表格的形式介绍下这 6 种协议，当然除了表中列出的这些协议之外还有很多协议，我们就挨个来简单对他们有一个了解和认识

![](https://res.cloudinary.com/incoder/image/upload/v1609258585/blog/license.png)

## 其他协议

### BY-NC-SA

你会发现每篇文章下面都有申明版权，这里使用的是 [<i class="fab fa-creative-commons"></i> BY-NC-SA](https://creativecommons.org/licenses/by-nc-sa/4.0/) 4.0 的协议，他们的含义如下
* <i class="fab fa-creative-commons"></i>：知识共享（CreativeCommons）
* NC：非商业性使用（NonCommercial），您不得将本作品用于商业目的
* SA：相同方式共享（ShareAlike），如果您再混合、转换或者基于本作品进行创作，您必须基于与原先许可协议相同的许可协议 分发您贡献的作品

使用此协议，您可以自由地
1. 共享 — 在任何媒介以任何形式复制、发行本作品
2. 演绎 — 修改、转换或以本作品为基础进行创作
>只要你遵守许可协议条款，许可人就无法收回你的这些权利

## 选择

上面说了那么多，有些协议并没有展开来说可能并不适用你当前的所需要选择的协议，那么你可以根据实际情况去筛选，可通过 https://choosealicense.com,  https://kaiyuanshe.cn/license-tool 这两个网站按照步骤去选择，最终确定协议即可

## 参考

1. [开源许可证选择器](https://kaiyuanshe.cn/license-tool/)
2. [Choose an open source license](https://choosealicense.com/)
3. [博云违反 Apache 2.0 开源协议被要求整改，开源协议到底应该如何遵守？](https://segmentfault.com/a/1190000022973105)
4. [开源协议是什么？有哪些？如何选择？](http://c.biancheng.net/view/2947.html)
5. [如何为你的代码选择一个开源协议](https://www.cnblogs.com/Wayou/p/how_to_choose_a_license.html)
6. [开源指北 Gitee](https://gitee.com/gitee-community/opensource-guide)