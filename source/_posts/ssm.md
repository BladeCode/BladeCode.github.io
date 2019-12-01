---
title: 构建基础SSM框架
date: 2018-05-20 09:39:10
categories: Frame
tag: 
  - Spring
  - SpringMVC
  - Mybatis
---

## SSM结构

![SSM](https://res.cloudinary.com/incoder/image/upload/v1528039004/blog/ssm-structure.png)

<!-- more -->

## SSM框架整合

所谓的SSM即：Spring，SpringMVC，Mybatis  
* [Spring](https://spring.io)：一个轻量级的框架，有很多的拓展功能，最主要的我们一般项目使用的就是IOC和AOP。
* [SpringMVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html)：Spring实现的一个Web层，相当于Struts的框架，但是比Struts更加灵活和强大.
* [Mybatis](http://www.mybatis.org/mybatis-3)：一个持久层的框架，在使用上相比Hibernate更加灵活，可以控制SQL的编写，使用 XML或注解进行相关的配置.

## 实战项目

![ssm-practice](https://res.cloudinary.com/incoder/image/upload/v1528647074/blog/ssm-practice.png)

项目功能：
1. Spring,SpringMVC,Mybatis框架整合
2. Create Features
3. Retrieve Features
4. Update Features
5. Delete Features

> 项目示例：[rc-ssm](https://github.com/RootCluster/rc-ssm/tree/example)

## 其他

### ajax之PUT请求

客户端ajax方式发送PUT请求，Tomcat默认不会对请求进行处理；
Tomcat：
1. 将请求体中的数据，封装成一个map
2. request.getParameter("fileName")就会从这个map中取值
3. springMVC封装POJO对象时，会把POJO中的属性的值，request.getParameter("fileName")

  解决方式：
  - 方式一：Ajax发送POST请求
  Ajax中type:"POST"
  data: $("").serialize()+"&_method=PUT"

  - 方式二：web配置中添加HttpPutFormContentFilter过滤器
  1.HttpPutFormContentFilter将请求体中的数据解析包装成一个map
  2.request被重新包装，request.getParameter()被重写，从自己封装的map中取出数据

### 获取属性的值

prop修改和读取DOM原生属性的值
attr修改和读取自定义属性的值
