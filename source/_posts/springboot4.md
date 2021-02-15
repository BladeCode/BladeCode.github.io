---
title: SpringBoot（四）配置文件
date: 2019-07-28 21:20:01
categories: SpringBoot
tag: [SpringBoot]
---

关于 SpringBoot 配置文件，在之前的文章中已经提到[配置文件格式](https://incoder.org/2019/06/23/springboot1/#%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F)，主要是两种格式的配置，这里并没有哪个配置写法一定优于另一种写法，对于配置文件名（application.yml 或者 application.properties），可以更改，为了减少不必要的麻烦，不建议修改，本篇文章以 yml 文件作为示例

<!-- more -->

本篇文章示例代码见：[springboot-config](https://github.com/RootCluster/rc-cluster-springboot/tree/master/springboot-config)

## YAML

[YAML](https://en.wikipedia.org/wiki/YAML)是JSON的超集，因此是用于指定分层配置数据的便捷格式。只要在类路径上有SnakeYAML库，SpringApplication类就会自动支持YAML作为属性的替代 。

### 语法规则

* 大小写敏感
* 缩进（只能使用空格，空格数量不重要）表示层级
* 注释用 `#` 符号

### 数据结构

1. 不可再分的单个的值，如数字，字符串等。
    ```yml
    env: dev
    crate-date: 2020
    is-mac: true

    # ~表示NULL值
    email: ~

    # 多行字符串可以使用 | 保留换行符，也可以使用 > 折叠换行
    # + 表示保留文字块末尾的换行，- 表示删除字符串末尾的换行
    message: |-
      hello world ${crate-date}

    # 单引号
    # 会转义特殊字符，特殊字符最终只是一个普通的字符串数据
    # 输出：mac \n catalina
    name1: 'mac \n catalina'

    # 双引号
    # 不会转义字符串里面的特殊字符，特殊字符会作为本身想表示的意思
    # 输出：mac
    # catalina
    name2: "mac \n catalina"
    ```
2. 数组，一组按次序排列的值
    ```yml
    # 这种写法，必须有两层结构，而且第二层（language 名字）是必须满足 Java 属性字段命名规则
    list:
      language:
        - 'object-c'
        - 'swift'
        - 'c'
    # 或者行内写法
    list-program-languages: object-c, swift, c
    # SpEL 获取数组
    el:
      list: object-c, swift, c
    ```
3. 对象，键值对的集合
    ```yml
    # 对象
    object:
      name: Jerry
      age: 20

    # 或者行内写法
    persons: { name: Jerry, age: 20 }

    # Map
    map-object:
      map:
        key1: value1
        key2: value2

    # 或者行内写法
    mapObjects.maps: { key1: value1,key2: value2 }
    ```
4. 随机数
    ```yml
    # 随机数
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

{% note warning %}
1. `:` 号后面有一个<font color="red">空格</font>
2. 对于复杂的数据结构（对象，List，Map），需要配套 <mark>@ConfigurationProperties</mark> 定义对应的对象
{% endnote %}

## 配置

为了在配置自定义属性时，向配置 SpringBoot 属性自动提示的功能，导入如下的包
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

|                         | @Value           | @ConfigurationProperties | @PropertySource             |
|-------------------------|------------------|--------------------------|-----------------------------|
| 使用场景                    | 单一属性注入，注解写在类的属性上 | 批量注入，注解写在类上              | 加载自定义配置文件，用于静态类获取配置文件中定义的信息 |
| 松散语法                    | 不支持              | 支持                       | 支持                          |
| SpEL                    | 支持               |  不支持                     | 支持                          |
| JSR-303 数据校验 @Validated | 不支持              |  支持                      | 支持                          |
| 复杂类型（数组，Map，对象等）        | 不完全支持（数组支持行内定义）  | 支持                       | 支持                          |

#### ConfigurationProperties

<mark>@ConfigurationProperties</mark> 注解是 SpringBoot提供的一种使用属性的注入方法，不仅可以方便的把配置文件中属性值与所注解类绑定，还支持松散绑定，JSR-303 数据校验等功能

```java
/**
 * 使用 @ConfigurationProperties 配置属性必须是小写，多单词之间可用'-'连接
 *
 * @author : Jerry xu
 * @since : 2020/3/29  16:26
 */
@Data
@Component
@ConfigurationProperties(prefix = "list")
public class ConfigListBean {

    /**
     * 这里不能再用 @Value("") 去加载，修饰当前的字段，
     * 必须指定其属性名和配置文件中定义的名称一致
     */
    private List<String> language;

}
```

#### Value

<mark>@Value</mark> 注解支持直接从配置文件中读取值，同时支持 [SpEL](https://docs.spring.io/spring/docs/4.2.x/spring-framework-reference/html/expressions.html) 表达式，但是不支持复杂数据类型和数据验证

使用 <mark>@Value</mark> 获取配置文件中定义的值，通常其类是被 <mark>@Controller</mark>，<mark>@Service</mark>，<mark>@Component</mark> 等注解修饰，如果是一般普通的类（如一些工具类）并 **<font color="red">不能</font>** 只接获取到配置文件中定义的值

```java
@Data
@Component
public class GradleDataBean {

    @Value("${env}")
    private String env;
    @Value("${crate-date}")
    private Integer createDate;
    @Value("${is-mac}")
    private Boolean isMac;
    @Value("${email}")
    private String email;
    @Value("${message}")
    private String message;
    @Value("${name1}")
    private String name1;
    @Value("${name2}")
    private String name2;

    @Value("${list-program-languages}")
    private List<String> programLanguages;
    
    /**
     * 使用el表达式，获取定义数组
     */
    @Value("#{'${el.list}'.split(',')}")
    private List<String> programList;

    @Value("${secret}")
    private String secret;
    @Value("${number}")
    private Integer number;
    @Value("${bignumber}")
    private Long bigNumber;
    @Value("${uuid}")
    private String uuid;
}
```

#### PropertySource

<mark>@PropertySource</mark> 注解加载自定义配置文件，由于 <mark>@PropertySource</mark> 指定的文件会优先加载，所以如果在 applocation.properties 文件中存在相同的属性配置，会覆盖前者中对应的值，且 <mark>@PropertySource</mark> 不支持 yml 文件注入

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

## 参考

* [介绍两种SpringBoot读取yml文件中配置数组的方法](https://blog.csdn.net/liujianyangbj/article/details/108810726)
* [Spring的@Value可以注入复杂类型吗？今天教你通过@value注入自定义类型](https://blog.csdn.net/liujianyangbj/article/details/111352703)
* [Properties and Configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html)