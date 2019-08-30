---
title: Lombok
date: 2019-08-21 22:00:46
categories: Util
tag: [Util]
---

在实际开发过程中，不管是服务端（Java），还是客户端（Android）都需要创建对应的实例bean对象，用来实例化对象，在对需要实例化的对象中，通常需要写 `set`，`get` 方法，字段少的时候，还能忍受，尤其当客户端字段多的时候，而且字段类型或者字段名称来回改动时，稍不注意，就很大机率修改不全面，就会造成一些隐藏bug，有没有不需要手动取写（快捷键生成）这些方法，当然有，本片文章，我们就来学习 [Lombok](https://projectlombok.org) 

Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more（Project Lombok是一个java库，可以自动插入编辑器并构建工具，为您的java增添色彩。 永远不要再写另一个getter或equals方法，使用一个注释，你的类有一个功能齐全的构建器，自动化你的日志记录变量等等），这是官方对Lombok的介绍，简单来讲就是通过注解的方式，代替一些重复性的代码，让代码更加简洁

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

Lombok提供稳定的注解，可以直接在生成环境使用，`org.projectlombok.lombok`包`lombok`路径下

### val

**val** 作为任何局部变量声明类型（包含 for语句声明），并且该类型会推断初始化表达式的类型，同时也是`final`的。

* 0.10版本加入该功能
* 使用场景：局部变量声明

```java
// x 将被推断为 double 类型，并且是final
val x = 10.0;
// y 将被推断为 ArrayList<String> 类型，并且是final
val y = new ArrayList<String>();
// z 将被转换为 final int z = 10;
val z = 10;
```

> 官方示例[val](https://projectlombok.org/features/val)

### var

**var** 作为任何局部变量声明类型（包含 for语句声明），并且该类型会推断初始化表达式的类型。

* 1.16.20版本加入该功能
* 使用场景：局部变量声明

```java
// x 将被推断为double，转换为 double x = 10.0d;
var x = 10.0;
```

> 官方示例[var](https://projectlombok.org/features/var)

### @NonNull

注解在 **属性** 上，会自动产生一个关于此参数的非空检查，如果参数为空，则抛出一个空指针异常，也会有一个默认的无参构造方法

* 0.11.10版本加入该功能
* 支持字段，方法，参数，局部变量，枚举

> 官方示例[@NonNull](https://projectlombok.org/features/NonNull)

### @Cleanup

自动资源管理，安全地调用close（）方法，确保通过调用 close() 方法清除你注释的变量声明，无论发生声明情况

* 支持局部变量

```java
public void copyFile(String in, String out) throws IOException {
    @Cleanup FileInputStream inStream = new FileInputStream(in);
    @Cleanup FileOutputStream outStream = new FileOutputStream(out);
    byte[] b = new byte[65536];
    while (true) {
        int r = inStream.read(b);
        if (r == -1) break;
        outStream.write(b, 0, r);
    }
}
```

> 官方示例[@Cleanup](https://projectlombok.org/features/Cleanup)

### @Getter/@Setter

* 注解在 **属性** 上；为单个属性提供 set/get 方法; 
* 注解在 **类** 上，为该类所有的属性提供 set/get 方法， 都提供默认构造方法
* 等级可自己更改 PUBLIC, MODULE, PROTECTED, PACKAGE, PRIVATE，默认Public

```java
// 可手动设置字段的set方法为你需要的修饰类型
@Setter(AccessLevel.PROTECTED) 
private String name;
```

> 官方示例[@Getter/@Setter](https://projectlombok.org/features/GetterSetter)

### @ToString

任何 **类** 定义都可以用 @ToString 注释，让lombok生成toString（）方法的实现。默认情况下，它会按顺序打印您的类名以及每个字段，并以逗号分隔

* includeFieldNames，默认true：打印时包括每个字段的名称
* callSuper，默认false：在输出中包含超类的实现的结果
* doNotUseGetters，默认false：通常情况下，如果有可用的getter，那么就会调用它们。要禁止此操作并让生成的代码直接使用这些字段，请将其设置为true
* onlyExplicitlyIncluded，默认false：仅包含用明确标记的字段和方法。通常，默认情况下包含所有（非静态）字段

```java
// 排除该字段一同生成在 toString()方法中
@ToString.Exclude 
private int id;
// 配置在toString中呈现此成员的行为；如果在方法上，请在输出中包含方法的返回值
// rank(默认为0)：首先打印更高的等级。相同级别的成员按照它们在源文件中出现的顺序打印
// name(默认为"")：默认为带注释的成员的 字段/方法 名称。如果名称等于默认包含字段的名称，则此成员将取代它
@ToString.Include(rank = 0, name = "")
private int id;
```

> 官方示例[@ToString](https://projectlombok.org/features/ToString)

### @EqualsAndHashCode

注解在 **类** 上, 可以生成 equals、canEqual、hashCode 方法，与 @Data 相比，少了 toString() 方法，部分属性和 @ToString 注解相同

> 官方示例[@EqualsAndHashCode](https://projectlombok.org/features/EqualsAndHashCode)

### Constructor

按照定制生成构造函数

* @NoArgsConstructor：生成不带参数的构造函数
* @RequiredArgsConstructor：生成带有必需参数的构造函数。必需参数是具有约束的最终字段和字段，例如@NonNull
* @AllArgsConstructor：生成一个全参构造函数。

> 官方示例[Constructor](https://projectlombok.org/features/constructor)

### @Data

生成所有字段的getter，一个有用的toString方法，以及检查所有非瞬态字段的hashCode和equals实现。还将为所有非最终字段以及构造函数生成setter

> 官方示例[@Data](https://projectlombok.org/features/Data)

### @Value

> 官方示例[@Value](https://projectlombok.org/features/Value)

### @Builder

> 官方示例[@Builder](https://projectlombok.org/features/Builder)

### @SneakyThrows

> 官方示例[@SneakyThrows](https://projectlombok.org/features/SneakyThrows)

### @Synchronized

> 官方示例[@Synchronized](https://projectlombok.org/features/Synchronized)

### @Getter(lazy=true)

> 官方示例[@GetterLazy](https://projectlombok.org/features/GetterLazy)

### @Log

> 官方示例[@log](https://projectlombok.org/features/log)

## 实验注解

Lombok提供实验性注解，请根据实际情况取舍，`org.projectlombok.lombok`包 `lombok.experimental`路径下

### @Accessors
### @ExtensionMethod
### @FieldDefaults
### @Delegate
### @Wither
### onMethod= / onConstructor= / onParam=
### UtilityClass
### Helper
### FieldNameConstants
### SuperBuilder

## 进阶配置

[Configuration system](https://projectlombok.org/features/configuration)