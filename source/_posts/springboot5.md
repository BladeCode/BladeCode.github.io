---
title: SpringBoot（五）注解
date: 2019-10-01 10:01:00
categories: SpringBoot
tag: [SpringBoot]
---

SpringBoot 与 SpringCloud 微服务技术栈体系本质就是围绕注解来展开，这些注解在微服务框架中扮演非常重要的角色，每个注解都有他的应用场景，通过一些注解的组合让 SpringBoot 与 SpringCloud 开发变的简单和高效，本篇文章我们就来汇总 SpringBoot 相关的注解

本篇文章基于如下版本
* Spring：5.1.8 RELEASE
* SpringBoot：2.1.6 RELEASE

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

说明子类可以继承父类中的该注解，注释类型是自动继承的。
* 如果注释类型声明中存在继承的元注释，并且用户在类声明中查询该注释类型，并且该类声明中没有该类型的注释，则将自动查询该类的超类以获取注释类型。重复此过程，直到找到该类型的注释，或到达类层次结构（对象）的顶部为止。
* 如果没有超类对此类型进行注释，则查询将指示所讨论的类没有此类注释。

>请注意，如果带注释的类型用于 **注释除类之外** 的任何内容，则此元注释类型无效。还要注意，此元注释仅使注释从超类继承；已实现的接口上的注释无效

* 路径：java.lang.annotation
* 引入：JDK 1.5 开始引入

### @Retention

注解的保留位置，RetentionPolicy 是提供的策略枚举
1. SOURCE：注解将被编译器丢弃，比如：@Override，@SupressWarnings
2. CLASS：注释将由编译器记录在类文件中，但不必在运行时由VM保留。这是默认的行为
3. RUNTIME：注释由编译器记录在类文件中，并由在运行时由VM保留，因此可以通过反射方式读取它们，比如	@Deprecated

>@Retention(RetentionPolicy.RUNTIME)   // 作用于运行期

* 路径：java.lang.annotation
* 引入：JDK 1.5 开始引入

### @Target

用于描述注解的使用范围（即：被描述的注解可以用在什么地方），ElementType 是提供的策略枚举
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

>@Target(ElementType.ANNOTATION_TYPE)   // 作用于注释类型

* 路径：java.lang.annotation
* 引入：JDK 1.5 开始引入

## Spring 相关

### annotation

#### @Bean

* 路径：org.springframework.context.annotation
* 引入：Spring 3.0 开始引入

#### @ComponentScan

@ComponentScan 是一个组合注解，默认会装配标识了@Controller，@Service，@Repository，@Component 注解到 Spring 容器中

* 路径：org.springframework.context.annotation
* 引入：Spring 3.1 开始引入

>@ComponentScan 与 @ComponentScans

#### @Conditional

@Conditional 注解可以根据代码中设置的条件装载不同的 bean，在设置条件注解之前，先要把装载的 bean 类去实现 Condition 接口，然后对该实现接口的类设置是否装载的条件。

SpringBoot 注解中的 @ConditionalOnProperty，@ConditionalOnBean 等以 @COnditional* 开头的注解，都是通过集成了@Conditional 来实现相应功能

* 路径：org.springframework.context.annotation
* 引入：Spring 4.0 开始引入

#### @Configuration

@Configuration 用于自定义配置类，可替换 XML 配置文件，被注解的类内部包含有一个或多个被 @Bean 注解的方法，这些方法会将被 AnnotationConfigApplicationContext 或  AnnotationConfigWebApplicationContext 类进行扫描，并用于构建 bean 定义，初始化 Spring 容器

* 路径：org.springframework.context.annotation
* 引入：Spring 3.0 开始引入

#### @DependsOn
#### @Description
#### @EnableAspectJAutoProxy
#### @EnableLoadTimeWeaving
#### @EnableMBeanExport

#### @Import

通过导入的方式实现把实例加入 SpringIOC 容器中。可以在需要时将没有被 Spring 容器管理的类导入至 Spring 容器中

* 路径：org.springframework.context.annotation
* 引入：Spring 3.0 开始引入


#### @ImportResource

和@Import 类似，区别就是@ImportResource 导入的是配置文件

* 路径：org.springframework.context.annotation
* 引入：Spring 3.0 开始引入

#### @Lazy

#### @Primary

#### @Profile

#### @PropertySource

>@PropertySource 与 @PropertySources

#### @Role

#### @Scope

### stereotype

* 路径：org.springframework.stereotype

#### @Component

@Component 是一个元注解，意思是可以注解其他类注解，比如：@Controller @Service @Repository 带此注解的类被看做组件，当使用基于注解的配置和类路径扫描的时候，这些类就会被实例化。其他类级别的注解也可以被认定为是一种特殊类型的组件，比如@Controller（注入服务），@Service（注入 DAO），@Repository（实现 DAO 访问）。

@Component 泛指组件，当组件不好归类时，我们可以使用这个注解进行标注，作用相当于 XML 配置，`<bean id="" class="" >`

* 路径：org.springframework.stereotype
* 引入：Spring 2.5 开始引入

#### @Controller

#### @Indexed

#### @Repository

#### @Service

## SpringMVC 相关

### @RequestMapping

### @RequestParam

### @PathVariable

## SpringBoot 相关

###  @SpringBootApplication

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

* 路径：org.springframework.boot.autoconfigure
* 引入：SpringBoot 1.2.0开始引入

###  @SpringBootConfiguration

###  @EnableAutoConfiguration

###  @ConditionalOnBean

###  @ConditionalOnMissingBean

###  @ConditionalOnClass

###  @ConditionalOnMissingClass

###  @ConditionalOnWebApplication

###  @ConditionalOnNotWebApplication

###  @ConditionalOnProperty

###  @ConditionalOnExpression

###  @ConditionalOnJava

###  @ConditionalOnResource

###  @ConditionalOnJndi

###  @ConditionalOnCloudPlatform

###  @ConditionalOnSingleCandidate

###  @ConfigurationProperties

###  @EnableConfigurationProperties

###  @AutoConfigureAfter

###  @AutoConfigureBefore

###  @AutoConfigureOrder

## SpringCloud 相关

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

## 异常

### @ControllerAdvice

### @ExceptionHandler

## 项目中实践

### @MappedSuperclass
### @Column
### @Getter和@Setter（Lombok）
### @PreUpdate和@PrePersist

## 其他注解

### @RestController
### @Autowired
### @PathVariable
### @JsonBackReference
### @RepositoryRestResourcepublic
### @ResponseBody
### @Value
### @Inject
### @Qualifier
### @Resource
### @RequestMapping

## 附录

* [Spring Boot 注解如何系统的学习](https://www.zhihu.com/question/324251802)
* [SpringBoot 系列（三）Spring Boot 自动配置](https://www.codingme.net/2019/01/springboot/springboot03-auto-config/)
