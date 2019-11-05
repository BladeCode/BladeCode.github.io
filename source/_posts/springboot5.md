---
title: SpringBoot（五）注解
date: 2019-10-01 10:01:00
categories: SpringBoot
tag: [SpringBoot]
---

SpringBoot 与 SpringCloud 这样的微服务技术栈体系本质就是围绕注解来展开，这些注解在微服务框架中扮演非常重要的角色，每个注解都有他独到的应用场景，通过一些注解的组合让 SpringBoot 与 SpringCloud 开发变的简单和高效，本篇文章我们就来汇总 SpringBoot 相关的注解

>由于本篇包含众多注解，请配合 `Ctrl` + `F` （或 `⌘` + `F`）使用
><font color=green>绿色</font> ：**基础** 的注解
><font color=red>红色</font> ：**常用** 的注解
><font color=blue>蓝色</font> ：常用的注解

由于 SpringBoot 的基础是 Spring，SpringBoot 相关部分注解都是在 Spring 的基础注解上的再组合，因此我们先来学习 Spring 的相关基础注解，在注解中大部分注解的修饰中包含已下几个注解，这里统一来说明下

## Java 相关

### @Documented

在默认情况下Documented注解表明这个注释是由 javadoc 记录的也有类似的记录工具。 如果一个类型声明被注释了文档化，它的注释成为公共API的一部分
* 路径：java.lang.annotation
* 引入：JDK 1.5 开始引入

### @Inherited

指示注释类型是自动继承的。如果注释类型声明中存在继承的元注释，并且用户在类声明中查询该注释类型，并且该类声明中没有该类型的注释，则将自动查询该类的超类以获取注释类型。重复此过程，直到找到该类型的注释，或到达类层次结构（对象）的顶部为止。如果没有超类对此类型进行注释，则查询将指示所讨论的类没有此类注释。请注意，如果带注释的类型用于注释除类之外的任何内容，则此元注释类型无效。还要注意，此元注释仅使注释从超类继承；已实现的接口上的注释无效
* 路径：java.lang.annotation
* 引入：JDK 1.5 开始引入

### @Retention

RetentionPolicy
1. SOURCE：注解将被编译器丢弃
2. CLASS：注释将由编译器记录在类文件中，但不必在运行时由VM保留。这是默认的行为
3. RUNTIME：注释由编译器记录在类文件中，并由在运行时由VM保留，因此可以通过反射方式读取它们
>@Retention(RetentionPolicy.RUNTIME)   // 作用于运行期
### @Target

用于描述注解的使用范围（即：被描述的注解可以用在什么地方）

ElementType
1. ANNOTATION_TYPE：用于注释类型
2. CONSTRUCTOR：用于描述构造器
3. FIELD：用于描述字段（包括枚举常量）
4. LOCAL_VARIABLE：用于描述局部变量
5. METHOD：用于描述方法
6. PACKAGE：用于描述包
7. PARAMETER：用于描述参数
8. TYPE：用于描述类、接口(包括注解类型) 或enum声明
9. TYPE_PARAMETER：用于参数类型
10. TYPE_USE：用于使用类型

## Spring 相关（基础注解）

### <font color=green>@Configuration</font>

* 路径：org.springframework.context.annotation
* 引入：Spring 3.0 开始引入

### <font color=green>@ComponentScan</font>

### <font color=green>@Conditional</font>

### <font color=green>@Import</font>

### <font color=green>@ImportResource</font>

### <font color=green>@Component</font>

## SpringBoot 相关

###  <font color=#FF0000>@SpringBootApplication</font> 

表示一个配置类，它声明一个或多个 Bean 方法并且会触发自动配置以及组件扫描，这是一个很便捷的注解，@SpringBootApplication 相当于同时使用 @Configuration、 @EnableAutoConfiguration、 @ComponentScan这三个注解

```java
@Target(ElementType.TYPE)             // 当前注解所修饰的对象范围：用于描述类，接口，enum声明
@Retention(RetentionPolicy.RUNTIME)   // 作用于运行期
@Documented                           // 生成文档
@Inherited                            // 继承
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {

}
```

>* 路径：org.springframework.boot.autoconfigure
>* 引入：SpringBoot 1.2.0开始引入

###  <font color=#FF0000>@SpringBootConfiguration</font> 

###  <font color=#FF0000>@EnableAutoConfiguration</font>

###  <font color=#FF0000>@ConditionalOnBean</font>

###  <font color=#FF0000>@ConditionalOnMissingBean</font>

###  <font color=#FF0000>@ConditionalOnClass</font>

###  <font color=#FF0000>@ConditionalOnMissingClass</font>

###  <font color=#FF0000>@ConditionalOnWebApplication</font>

###  <font color=#FF0000>@ConditionalOnNotWebApplication</font>

###  <font color=#FF0000>@ConditionalOnProperty</font>

###  <font color=#FF0000>@ConditionalOnExpression</font>

###  <font color=#FF0000>@ConditionalOnJava</font>

###  <font color=#FF0000>@ConditionalOnResource</font>

###  <font color=#FF0000>@ConditionalOnJndi</font>

###  <font color=#FF0000>@ConditionalOnCloudPlatform</font>

###  <font color=#FF0000>@ConditionalOnSingleCandidate</font>

###  <font color=#FF0000>@ConfigurationProperties</font>

###  <font color=#FF0000>@EnableConfigurationProperties</font>

###  <font color=#FF0000>@AutoConfigureAfter</font>

###  <font color=#FF0000>@AutoConfigureBefore</font>

###  <font color=#FF0000>@AutoConfigureOrder</font>

## SpringMVC 相关

### @RequestMapping

### @RequestParam

### @PathVariable

## 异常

### @ControllerAdvice

### @ExceptionHandler

## 项目中实践

### @MappedSuperclass
### @Column
### @Getter和@Setter（Lombok）
### @PreUpdate和@PrePersist

## JPA 注解

### @Entity
### @MappedSuperClass
### @NoRepositoryBean
### @Column
### @Id
### @GeneratedValue
### @SequenceGeneretor
### @Transient
### @JsonIgnore
### @JoinColumn
### @OneToOne、@OneToMany、@ManyToOne

## 其他注解

### @RestController
### @Autowired
### @PathVariable
### @JsonBackReference
### @RepositoryRestResourcepublic
### @ResponseBody
### @Controller
### @Service
### @Repository
### @Bean
### @Value
### @Inject
### @Qualifier
### @Resource
### @RequestMapping
### @RequestMapping

## 附录

* [Spring Boot 注解如何系统的学习](https://www.zhihu.com/question/324251802)
* [Springboot 系列（三）Spring Boot 自动配置](https://www.codingme.net/2019/01/springboot/springboot03-auto-config/)
