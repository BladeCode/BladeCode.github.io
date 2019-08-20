---
title: SpringBoot（四）配置文件
date: 2019-07-28 21:20:01
categories: SpringBoot
tag: [SpringBoot]
---

关于 springboot 配置文件，在之前的文章中已经提到[配置文件格式](https://incoder.org/2019/06/23/springboot1/#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F)，主要是两个格式的配置，这里并没有哪个配置写法一定优于另一种写法，对于配置文件名（application.yml 或者 application.properties），可以更改，为了减少不必要的麻烦，不建议修改，本篇文章以 yml 文件作为示例

## YAML

YAML是JSON的超集，因此是用于指定分层配置数据的便捷格式。只要在类路径上有SnakeYAML库，SpringApplication类就会自动支持YAML作为属性的替代 。

### 语法规则

* 大小写敏感
* 缩进（只能使用空格，空格数量不重要）表示层级
* 注释用 `#` 符号

### 数据结构

1. 变量，不可再分的单个值，如数组，字符串等
    ```yml
    name: Jerry
    age: 20
    # ~表示NULL值
    email: ~
    # 多行字符串可以使用 | 保留换行符，也可以使用 > 折叠换行
    # + 表示保留文字块末尾的换行，- 表示删除字符串末尾的换行
    message: |-
      hello world
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
      secret: $ {random.value}
      number: $ {random.int}
      bignumber: $ {random.long}
      uuid: $ {random.uuid}
      number.less.than.ten: $ {random.int（10）}
      number.in.range: $ {random.int [1024,65536]}
    ```
5. 默认值，占位符获取之前配置的值，如果没有可以是用:指定默认值
   ```yml
   bootapp.name: SpringBoot
     description: ${bootapp.name}是一个spring应用程序
   ```

> `:` 号后面有空格，YAML 文件不能使用`@PropertySource`注解

## 配置

### 默认配置

### 自定义配置

1. 首先在 application.yml 文件中进行自定义字段的声明，如下写法
    ```yml
    myConfig:
    myObject:
        myName: zhangsan
        myAge: 20
    ```


### 多环境配置

## 配置文件加载顺序

Spring Boot 启动会扫描以下位置的配置文件（application.properties application.yml） 作为Spring Boot 的默认配置文件

* -file:./config/
* -file:./
* -classpath:/config/
* -classpath:/

优先级从高到低，高优先级的配置会覆盖低优先级的配置



## 附录
* [Spring Boot 配置文件](https://www.codingme.net/2019/01/springboot/springboot02-config/)
* [最全面的SpringBoot配置文件详解](https://zhuanlan.zhihu.com/p/57693064)
* [Properties and Configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html)