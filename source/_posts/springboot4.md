---
title: SpringBoot（四）配置文件
date: 2019-07-28 21:20:01
categories: SpringBoot
tag: [SpringBoot]
---

关于 SpringBoot 配置文件，在之前的文章中已经提到[配置文件格式](https://incoder.org/2019/06/23/springboot1/#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F)，主要是两种格式的配置，这里并没有哪个配置写法一定优于另一种写法，对于配置文件名（application.yml 或者 application.properties），可以更改，为了减少不必要的麻烦，不建议修改，本篇文章以 yml 文件作为示例

<!-- more -->

## YAML

[YAML](https://en.wikipedia.org/wiki/YAML)是JSON的超集，因此是用于指定分层配置数据的便捷格式。只要在类路径上有SnakeYAML库，SpringApplication类就会自动支持YAML作为属性的替代 。

### 语法规则

* 大小写敏感
* 缩进（只能使用空格，空格数量不重要）表示层级
* 注释用 `#` 符号

### 数据结构

1. 变量，不可再分的单个的值，如数字，字符串等。
    ```yml
    name: Jerry
    age: 20
    # ~表示NULL值
    email: ~
    # 多行字符串可以使用 | 保留换行符，也可以使用 > 折叠换行
    # + 表示保留文字块末尾的换行，- 表示删除字符串末尾的换行
    message: |-
      hello world
    # 双引号
    # 不会转义字符串里面的特殊字符，特殊字符会作为本身想表示的意思
    # 输出：
    # A
    # B
    name1: "A n B"
    # 单引号  
    # 会转义特殊字符，特殊字符最终只是一个普通的字符串数据
    # 输出：
    # A n B
    name2: 'A n B'
    ```
2. 数组，一组按次序排列的值
   ```yml
   lang:
    - java
    - golang
    - c
    # 或者行内写法
    lang: [java, golang, c]
   ```
3. 对象，键值对的集合
    ```yml
    person:
      name: Jerry
      age: 20
    # 或者行内写法
    person: {name: Jerry, age: 20}
    ```
4. 随机数
    ```yml
    bootapp
      secret: ${random.value}
      number: ${random.int}
      bignumber: ${random.long}
      uuid: ${random.uuid}
    ```
5. 默认值，占位符获取之前配置的值，如果没有可以是用:指定默认值
   ```yml
   bootapp.name: SpringBoot
     description: ${bootapp.name}是一个spring应用程序
   ```

> `:` 号后面有空格，YAML 文件不能使用`@PropertySource`注解

## 配置

为了在配置自定义属性时，向配置 springboot 属性自动提示的功能，导入如下的包
```xml
<!--导入配置文件处理器，配置文件进行绑定就会有提示-->
<!-- @ConfigurationProperties annotation processing (metadata for IDEs)
         生成spring-configuration-metadata.json类，需要引入此类-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure</artifactId>
</dependency>
```
或者在 gradle 配置文件中添加依赖
```gradle
compileOnly 'org.springframework.boot:spring-boot-configuration-processor'
annotationProcessor "org.springframework.boot:spring-boot-configuration-processor"
```
如果任然无法自动提示，请查看你的编辑器 IDEA 中是否开启了 `Annonation Processing`
![idea-annotation-processors](https://res.cloudinary.com/incoder/image/upload/v1566701581/blog/idea-annotation-processors.png)

### 定义配置

#### 默认配置

[Common application properties](https://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html)，是 SpringBoot 官方提供的一些默认配置属性说明，如果需要更改，只需要在 application.yml 文件中重写该属性即可，比如重新设置服务的启动端口为 9090
```yml
server:
  port: 9090
```

#### 自定义配置
首先在 application.yml 文件中根据上面所述的规则写法进行自定义字段的声明
```yml
myConfig:
  maps:
    key: value
```

### 配置的使用

#### ConfigurationProperties

`@ConfigurationProperties` 注解是 SpringBoot提供的一种使用属性的注入方法，不仅可以方便的把配置文件中属性值与所注解类绑定，还支持松散绑定，JSR-303 数据校验等功能

#### Value

`@Value` 注解支持直接从配置文件中读取值，同时支持 [SpEL](https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/expressions.html) 表达式，但是不支持复杂数据类型和数据验证

#### PropertySource

`@PropertySource` 注解加载自定义配置文件，由于 `@PropertySource` 指定的文件会优先加载，所以如果在 `applocation.properties` 文件中存在相同的属性配置，会覆盖前者中对应的值，且 `@PropertySource` 不支持 yml 文件注入

### 多环境配置

在实际的开发中，不同环境对应不同的配置，因此我们需要根据环境来配置不同的项目配置信息，SpringBoot 也是支持我们进行多环境的配置，通常情况下，我们命名为 `application-{profile}.properties/yml` ，其中
`{profile}`表示不同的环境，比如：dev（开发），prod（线上） 等

关于具体的多环境配置，可以参考文章 [SpringBoot（五）多环境配置](https://incoder.org/2020/02/02/springboot5/)

## 配置文件加载顺序

Spring Boot 启动会扫描以下位置的配置文件（application.properties 或 application.yml） 作为Spring Boot 的默认配置文件

1. -file:./config/
2. -file:./
3. -classpath:/config/
4. -classpath:/

加载顺序可以查看下图
![application-sort](https://res.cloudinary.com/incoder/image/upload/v1585239269/blog/application-sort.png)

优先级从高到低，高优先级的配置会覆盖低优先级的配置

## 附录

* [最全面的SpringBoot配置文件详解](https://zhuanlan.zhihu.com/p/57693064)
* [Properties and Configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html)