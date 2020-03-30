---
title: SpringBoot（八）SpringApplication 源码分析
date: 2020-02-26 09:10:00
categories: SpringBoot
tag: [SpringBoot]
---

正如我们看到的 SpringBoot 应用启动入口类，main() 方法中一行简单 `SpringApplication.run(MyApplication.class, args);` 就可以将 SpringBoot 应用给启动了。那么它肯定是在`SpringApplication`中做了大量的工作，才能将应用启动，因此本篇文章我们来一起看看这个核心的类

SpringApplication 类可以从 Java 的 main 方法中引导和启动 Spring 的应用，默认情况下它会按照如下的启动步骤
1. 创建一个恰当的 ApplicationContext 实例（取决于你的 classpath 路径）
2. 注册一个 CommandLinePropertySource 将命令行参数作为 Spring 的属性（换句话说，可以通过命令行来传递当前应用所需要的一些属性）
3. 刷新应用的 Context（内容上下文），并加载所有单例的 beans
4. 触发每个 CommandLineRunner beans

<!-- more -->

大多数情况下，我们在 main() 方法中直接使用静态的 run(Class, String[]) 方法启动加载应用，对于一些高级的配置，我们可以创建一个 `SpringApplication` 实例，在运行之前进行自定义的配置
```java
public static void main(String[] args) {
    SpringApplication application = new SpringApplication(MyApplication.class);
    // ... customize application settings here
    application.run(args)
}
```

`SpringApplication` 可以读取不同来源的 bean 信息，通常推荐我们使用被 `@Configuration` 修饰的单例类来启动应用，然而你也可以在多种来源去设置你的 source
* 使用`AnnotatedBeanDefinitionReader`加载完全限定类名
* 使用`XmlBeanDefinitionReader`读取本地 XML 资源，或者使用`GroovyBeanDefinitionReader` 读取 Groovy 脚本去加载
* 使用`ClassPathBeanDefinitionScanner`扫描得到包的名字