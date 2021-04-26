---
title: SpringBoot（九）普通类如何获取配置文件中的值
date: 2020-06-26 07:10:00
categories: SpringBoot
tag: [SpringBoot]
---

在之前的 SpringBoot 学习中，我们知道，可以在项目的 `application.yml` 文件或 `application.properties` 文件中获取到一些我们项目中的一些静态值。但对于一些非 Spring 所管理的类（比如一些工具类）该如何获取到定义在配置文件中的值呢？而这些工具类中的配置值和对应项目运行的环境有关，我们如果固定写在代码内，每次打包时去更改，显然这种做法不够好，那么本篇文章就来实践一些非 Spring 类如何获取配置的值

<!-- more -->

在[《SpringBoot（四）配置文件》](https://incoder.org/2019/07/28/springboot4) 文章中提到，在使用 `@Value` 注解时，当时提到说 “使用 `@Value` 获取配置文件中定义的值，通常其类是被 <font color="red">`@Controller`，`@Service` ，`@Component`</font> 等注解修饰，如果是一般普通的类（如一些工具类）并 **不能** 只接获取到配置文件中定义的值”，显然我们直接按照之前的套路，在`application.yml`等配置文件中添加自定义的配置，使用 `@Value` 注解在我们的普通类中是无法获取到的，其实要解决这个问题很简单，那就是将当前的普通类变成一个被 Spring 所接管的类（普通类上使用 `@Component` 来修饰），这样我们就可以使用了

就这？你以为就结束了，那我还写个啥笔记，你在操作完发现你依然拿不到在配置文件中定义的静态值，接下来让我一步步来告诉你怎么来写，和为啥会这么做的原因


### 配置文件

## 参考

1. [工具类用单例模式还是静态方法](https://blog.csdn.net/huantai3334/article/details/104504208)
2. [springboot 工具类加载配置对象](https://blog.csdn.net/sunnyzyq/article/details/102457434)
3. [静态方法(工具类)中调用Spring管理的Bean](https://blog.csdn.net/xiangwang2016/article/details/106148880)
4. [spring实现静态注入](https://segmentfault.com/a/1190000019844427)
5. [工具类该用单例模式，还是用静态的方式](https://blog.csdn.net/qq_35584878/article/details/91438648)
6. [单例模式工具类中Spring 注入为空 的一些小问题](https://blog.csdn.net/jzy2046/article/details/89948124)