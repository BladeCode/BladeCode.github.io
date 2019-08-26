---
title: Lombok
date: 2019-08-21 22:00:46
categories: Util
tag: [Util]
---

在实际开发过程中，不管是服务端（Java），还是客户端（Android）都需要创建对应的实例bean对象，用来实例化对象，在对需要实例化的对象中，通常需要写 `set`，`get` 方法，字段少的时候，还能忍受，尤其当客户端字段多的时候，而且字段类型或者字段名称来回改动时，稍不注意，就很大机率修改不全面，就会造成一些隐藏bug，有没有不需要手动取写（快捷键生成）这些方法，当然有，本片文章，我们就来学习 [Lombok](https://projectlombok.org) 

Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more（Project Lombok是一个java库，可以自动插入编辑器并构建工具，为您的java增添色彩。 永远不要再写另一个getter或equals方法，使用一个注释，你的类有一个功能齐全的构建器，自动化你的日志记录变量等等），这是官方对Lombok的介绍，简单来讲就是通过注解的方式，代替生产 `set`，`get` 方法

## 安装集成

* 编辑器（IDEA or Android Studio）安装 Lombok 插件，`File` -> `Settings...` -> `Plugins`
* 项目集成Lombok依赖，项目按照不同的包管理， 按照对应方式添加包依赖
    ```xml
    <!-- gradle 集成 -->
    <!-- https://projectlombok.org/setup/gradle -->
    repositories {
        mavenCentral()
    }

    dependencies {
        implementationOnly 'org.projectlombok:lombok:1.18.8'
        annotationProcessor 'org.projectlombok:lombok:1.18.8'
    }

    <!-- maven集成 -->
    <!-- https://projectlombok.org/setup/maven -->
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.8</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

## 稳定注解

Lombok提供稳定的注解，可以直接在生成环境使用，包路径

### val

### var

### @NonNull

注解在 属性 上，会自动产生一个关于此参数的非空检查，如果参数为空，则抛出一个空指针异常，也会有一个默认的无参构造方法

### @Cleanup

### @Getter/@Setter

* 注解在 **属性** 上；为单个属性提供 set 方法; 
* 注解在 **类** 上，为该类所有的属性提供 set 方法， 都提供默认构造方法

### @ToString

这个注解用在 类 上，可以生成所有参数的 toString 方法，还会生成默认的构造方法

### @EqualsAndHashCode

注解在 类 上, 可以生成 equals、canEqual、hashCode 方法

### @NoArgsConstructor, @RequiredArgsConstructor and @AllArgsConstructor

### @Data

### @Value

### @Builder

### @SneakyThrows

### @Synchronized

### @Getter(lazy=true)

### @Log

### experimental

## 实验注解

###  