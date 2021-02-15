---
title: SpringBoot（二） 启动分析JarLauncher
date: 2019-07-05 11:10:11
categories: SpringBoot
tag: [SpringBoot]
---

我们在开发过程中，使用 {% label info@java -jar you-jar-name.jar %} 命令来启动应用，它是如何启动？以及它如何去寻找 `.class` 文件并执行这些文件？本节就带着这两个问题，让我们一层层解开 SpringBoot 项目的 jar 启动过程，废话不多说，跟着我的脚步一起去探索 {% label danger@spring-boot-load %} 的秘密。

在 [SpringBoot（一）初识](https://incoder.org/2019/06/23/springboot1/) 已经解释了为什么在编译后的 jar 中根目录存在 **<font color="red">org/springframework/boot/loader</font>** 内容，以及为了方便学习研究，我们需要在项目的依赖中导入 {% label success@org.springframework.boot:spring-boot-loader %} 依赖。同时我们在解压的 {% label info@you-jar-name.jar %} 文件中，查看对应的清单文件 {% label primary@MANIFEST.MF %} 内容，其中明确指出了应用的入口 **{% label @org.springframework.boot.loader.JarLauncher %}** 因此我们就从 **<font color="red">JarLauncher</font>** 开始一步步深入

<!-- more -->

![spring-boot-loader-jarlauncher](https://res.cloudinary.com/incoder/image/upload/v1562394534/blog/spring-boot-loader-jarlauncher.png)

## 结构

先用Diagrams来表述 **<font color="red">JarLauncher</font>** 类之间的结构及方法等相关信息
![jarlauncher](https://res.cloudinary.com/incoder/image/upload/v1562399159/blog/jarlauncher.png)

从Diagrams可知
* 继承关系：JarLauncher <mark>extends</mark> ExecutableArchiveLauncher <mark>extends</mark> Launcher
* 启动入口：JarLauncher {% label success@main %} 方法

>关于图上图标含义，这里就不再赘述，烦请移步 [IntelliJ IDEA Icon reference](https://www.jetbrains.com/help/idea/symbols.html)

## 流程分析

### jar规范

对于 Java 标准的 jar 文件来说，规定在一个 jar 文件中，我们必须要将指定 {% label success@main.class %} 的类直接放置在文件的顶层目录中（也就是说，它不予许被嵌套），否则将无法加载，对于 BOOT-INF/class/ 路径下的 `class` 因为不在顶层目录，因此也是无法直接进行加载， 而对于 BOOT-INF/lib/ 路径的 jar 属于嵌套的（Fatjar），也是不能直接加载，因此 Spring 要想启动加载，就需要自定义实现自己的类加载器去加载。

>关于 jar **官方标准**说明请移步
>* [JAR File Specification](https://docs.oracle.com/javase/8/docs/technotes/guides/jar/jar.html#Signed_JAR_File)
>* [JAR (file format)](https://en.wikipedia.org/wiki/JAR_(file_format))

### 源码分析

#### main 方法

根据清单文件 {% label primary@MANIFEST.MF %} 中  {% label warning@Main-Class %} 的描述，我们知道入口类就是 **<font color="red">JarLauncher</font>**；先看下这个类的 javadoc 介绍

```java
/**
 * {@link Launcher} for JAR based archives. This launcher assumes that dependency jars are
 * included inside a {@code /BOOT-INF/lib} directory and that application classes are
 * included inside a {@code /BOOT-INF/classes} directory.
 * 
 * 用于基于JAR的归档。这个启动程序假设依赖jar包含在{@code /BOOT-INF/lib}目录中，
 * 应用程序类包含在{@code /BOOT-INF/classes}目录中
 *
 * @author Phillip Webb
 * @author Andy Wilkinson
 */
```

紧接着，要进行源码分析，那肯定是找到入口，一步步深入，那么对于 **<font color="red">JarLauncher</font>** 就是它的 {% label success@main %} 方法了

```java
public static void main(String[] args) throws Exception {
    // launch 方法是调用父类 Launcher 的 launch 方法
    new JarLauncher().launch(args);
}
```

那我们去看一看 Launcher 的 {% label success@launch %} 方法

```java
/**
 * Launch the application. This method is the initial entry point that should be
 * called by a subclass {@code public static void main(String[] args)} method.
 * 
 * 启动一个应用，这个方法应该被初始的入口点，这个入口点应该是一个Launcher的子类的 
 * public static void main(String[] args)这样的方法调用
 *
 * @param args the incoming arguments
 * @throws Exception if the application fails to launch
 */
protected void launch(String[] args) throws Exception {
    // 1. 注册一些 URL的属性
    JarFile.registerUrlProtocolHandler();
    // 2. 创建类加载器（LaunchedURLClassLoader），加载得到集合要么是BOOT-INF/classes/
    //    或者BOOT-INF/lib/的目录或者是他们下边的class文件或者jar依赖文件
    ClassLoader classLoader = createClassLoader(getClassPathArchives());
    // 3. 启动给定归档文件和完全配置的类加载器的应用程序
    launch(args, getMainClass(), classLoader);
}
```

#### getClassPathArchives 方法

{% label success@launch %} 方法的第一步的相关内容比较简单，这里不做过多说明，主要后面两步，我们先看第二步，创建一个类加载器（ClassLoader），其中 getClassPathArchives() 方法是一个抽象方法，具体的实现有（<mark>ExecutableArchiveLauncher</mark> 和 {% label default@PropertiesLauncher %} ，因为我们研究的 <font color="red">JarLauncher</font> 是继承 <mark>ExecutableArchiveLauncher</mark> ，因此我们这里看 <mark>ExecutableArchiveLauncher</mark> 类中 getClassPathArchives() 方法的实现）我们要看看这个方法中它做了什么

```java
@Override
protected List<Archive> getClassPathArchives() throws Exception {
    // 得到一个Archive的集合（BOOT-INF/classes/）和（BOOT-INF/lib/）目录所有的文件
    //     a. this.archive 中当前类的 archive 是怎么来的？
    //     b. getNestedArachives()是如何获得一个嵌套的 jar 归档？
    //     c. this::isNestedArchive 这个方法引用它做了什么？
    List<Archive> archives = new ArrayList<>(this.archive.getNestedArchives(this::isNestedArchive));
    // 一个事后处理的方法
    postProcessClassPathArchives(archives);
    return archives;
}
```

{% label primary@this.archive %} 位于当前类 <mark>ExecutableArchiveLauncher</mark> 的构造方法中

```java
public ExecutableArchiveLauncher() {
    try {
        // 调用 createArchive() 方法得到Archive
        this.archive = createArchive();
    }
    catch (Exception ex) {
        throw new IllegalStateException(ex);
    }
}

/////////////////////////////////////////////////////////////
// 紧接着我们查看 createArchive() 方法都做了什么                //
/////////////////////////////////////////////////////////////

// Launcher.class 中的 createArchive()方法
// 得到我们运行文件的Archive相关的信息
protected final Archive createArchive() throws Exception {
    ProtectionDomain protectionDomain = getClass().getProtectionDomain();
    CodeSource codeSource = protectionDomain.getCodeSource();
    URI location = (codeSource != null) ? codeSource.getLocation().toURI() : null;
    String path = (location != null) ? location.getSchemeSpecificPart() : null;
    if (path == null) {
	    throw new IllegalStateException("Unable to determine code source archive");
    }
    // 返回我们要执行的jar文件的绝对路径(java -jar xxx.jar中 xxx.jar的绝对路径)
    File root = new File(path);
    if (!root.exists()) {
	    throw new IllegalStateException("Unable to determine code source archive from " + root);
    }
    return (root.isDirectory() ? new ExplodedArchive(root) : new JarFileArchive(root));
}
```

对于 getNestedArachives() 方法，它是 Archive 的接口

```java
/**
 * Returns nested {@link Archive}s for entries that match the specified filter.
 * 
 * 返回与过滤器相匹配的嵌套归档文件
 * 
 * @param filter the filter used to limit entries
 * @return nested archives
 * @throws IOException if nested archives cannot be read
 */
List<Archive> getNestedArchives(EntryFilter filter) throws IOException;

/////////////////////////////////////////////////////////////
// 紧接着我们查看 getNestedArchives() 的实现                   //
/////////////////////////////////////////////////////////////

// 这里的参数 EntryFilter类型中有一个 matches(Entry entry) 方法，
// 这也是this::isNestedArchive所对应的实际方法
@Override
public List<Archive> getNestedArchives(EntryFilter filter) throws IOException {
    List<Archive> nestedArchives = new ArrayList<>();
    for (Entry entry : this) {
        if (filter.matches(entry)) {
            nestedArchives.add(getNestedArchive(entry));
        }
    }
    return Collections.unmodifiableList(nestedArchives);
}
```

而 {% label primary@this::isNestedArchive %} 方法引用，我们查看 `isNestedArchive` 抽象方法

```java
/**
 * Determine if the specified {@link JarEntry} is a nested item that should be added
 * to the classpath. The method is called once for each entry.
 * 
 * 确定指定的{@link JarEntry}是否是应该添加到类路径的嵌套项。对每个条目调用该方法一次
 * 
 * @param entry the jar entry
 * @return {@code true} if the entry is a nested item (jar or folder)
 */
protected abstract boolean isNestedArchive(Archive.Entry entry);

/////////////////////////////////////////////////////////////
// 紧接着我们查看 isNestedArchive() 实现                      //
/////////////////////////////////////////////////////////////

// JarLauncher.class 中的 isNestedArchive()方法
@Override
protected boolean isNestedArchive(Archive.Entry entry) {
    // 如果是目录判断是不是BOOT-INF/classes/目录
    if (entry.isDirectory()) {
        return entry.getName().equals(BOOT_INF_CLASSES);
    }
    // 如果是文件判断文件的前缀是不是BOOT-INF/lib/开头
    return entry.getName().startsWith(BOOT_INF_LIB);
}
```

#### createClassLoader 方法

把符合条件的 Archives 作为参数传入到 createClassLoader() 方法，创建一个类加载器，我们跟进去，查看 createClassLoader() 方法

```java
/**
 * Create a classloader for the specified archives.
 *
 * 创建一个所指定归档文件的类加载器
 *
 * @param archives the archives
 * @return the classloader
 * @throws Exception if the classloader cannot be created
 */
protected ClassLoader createClassLoader(List<Archive> archives) throws Exception {
    List<URL> urls = new ArrayList<>(archives.size());
    // 遍历传进来的 archives，将每一个 Archive 的 URL（归档文件在磁盘上的完整路径）添加到 urls 集合中
    for (Archive archive : archives) {
        urls.add(archive.getUrl());
    }
    // 
    return createClassLoader(urls.toArray(new URL[0]));
}


/**
 * Create a classloader for the specified URLs.
 *
 * 创建指定 URL 的类加载器
 *
 * @param urls the URLs
 * @return the classloader
 * @throws Exception if the classloader cannot be created
 */
protected ClassLoader createClassLoader(URL[] urls) throws Exception {
    // 这里的 LaunchedURLClassLoader 是 SpringBoot loader 给我们提供的一个全新的类加载器
    // 参数 urls 是 class 文件或者资源配置文件的路径地址
    // 参数 getClass().getClassLoader() 是应用类加载器
    return new LaunchedURLClassLoader(urls, getClass().getClassLoader());
}


/**
 * Create a new {@link LaunchedURLClassLoader} instance.
 * @param urls the URLs from which to load classes and resources
 * @param parent the parent class loader for delegation
 */
public LaunchedURLClassLoader(URL[] urls, ClassLoader parent) {
    super(urls, parent);
}
```

super() 方法是调用父类的方法，这样一层层跟进去，最终到了 JDK 的 `ClassLoader` 类，它也是所有类加载器的顶类

#### launch 方法

{% label success@launch %} 方法的第二个参数，getMainClass() 是一个抽象方法

```java
/**
 * Returns the main class that should be launched.
 * @return the name of the main class
 * @throws Exception if the main class cannot be obtained
 */
protected abstract String getMainClass() throws Exception;

/////////////////////////////////////////////////////////////
// 紧接着我们查看 getMainClass() 实现                         //
/////////////////////////////////////////////////////////////

@Override
protected String getMainClass() throws Exception {
    Manifest manifest = this.archive.getManifest();
    String mainClass = null;
    if (manifest != null) {
        // 获取到 Manifest 文件中属性为`Start-Class`对应的值，也就是当前项目工程启动的类的完整路径
        mainClass = manifest.getMainAttributes().getValue("Start-Class");
    }
    if (mainClass == null) {
        throw new IllegalStateException("No 'Start-Class' manifest entry specified in " + this);
    }
    return mainClass;
}
```

接着我们看 {% label success@launch %} 方法

```java
/**
 * Launch the application given the archive file and a fully configured classloader.
 *
 * 加载指定存档文件和完全配置的类加载器的应用程序
 *
 * @param args the incoming arguments
 * @param mainClass the main class to run
 * @param classLoader the classloader
 * @throws Exception if the launch fails
 */
protected void launch(String[] args, String mainClass, ClassLoader classLoader) throws Exception {
    // 将应用的加载器换成了自定义的 LaunchedURLClassLoader 加载器，然后入到线程类加载器中
    // 最终在未来的某个地方，通过线程的上下文中取出类加载进行加载
    Thread.currentThread().setContextClassLoader(classLoader);
    // 创建一个主方法运行器运行
    createMainMethodRunner(mainClass, args, classLoader).run();
}

/**
 * Create the {@code MainMethodRunner} used to launch the application.
 *
 * 创建一个 MainMethodRunner 用于启动这个应用
 *
 * @param mainClass the main class
 * @param args the incoming arguments
 * @param classLoader the classloader
 * @return the main method runner
 */
protected MainMethodRunner createMainMethodRunner(String mainClass, String[] args, ClassLoader classLoader) {
    return new MainMethodRunner(mainClass, args);
}
```

返回一个 `MainMethodRunner` 对象，我们紧接着去看看这个对象，

```java
/**
 * Utility class that is used by {@link Launcher}s to call a main method. The class
 * containing the main method is loaded using the thread context class loader.
 * 
 * 被 Launcher 使用来调用 main 方法的辅助类，使用线程类加载来加载包含 main 方法的类
 *
 * @author Phillip Webb
 * @author Andy Wilkinson
 */
public class MainMethodRunner {

    private final String mainClassName;

    private final String[] args;

    /**
     * Create a new {@link MainMethodRunner} instance.
     * @param mainClass the main class
     * @param args incoming arguments
     */
    public MainMethodRunner(String mainClass, String[] args) {
        this.mainClassName = mainClass;
        this.args = (args != null) ? args.clone() : null;
    }

    // 关键方法
    public void run() throws Exception {
        // 获取到当前线程上下文的类加载器，实际就是 springboot 自定义的加载器（LaunchedURLClassLoader）
        // 加载 this.mainClassName所对应的类，实际也就是清单文件中对应 Start-Class 属性的类
        Class<?> mainClass = Thread.currentThread().getContextClassLoader().loadClass(this.mainClassName);
        // 通过反射获取到 main 方法和参数
        Method mainMethod = mainClass.getDeclaredMethod("main", String[].class);
        // 调用目标方法运行
        // invoke 方法参数一：是被调用方法所在对象，这里为 null，原因是我们所调用的目标方法是一个静态方法
        // invoke 方法参数二：被调用方法所接收的参数
        mainMethod.invoke(null, new Object[] { this.args });
    }

}
```

到此为止，invoke 方法成功调用，那么我们项目中的main 方法就执行了，这时我们的所编写的 springboot 应用就正式的启动了。那么关于 springboot 的 loader 加载过程已经分析完

## 总结

![summary-jarlauncher](https://res.cloudinary.com/incoder/image/upload/v1604881134/blog/summary-jarlauncher.jpg)

从 jar 规范的角度出发，我们深入分析了 springboot 项目启动的整个过程，这个过程到底对不对，我们口说无凭，需要实际检验我们分析  
首先，我们先思考，项目的应用启动入口是不是必须是 {% label success@main.class %} 方法，以及为什么要默认这么做？
其次，我们再思考，在编辑器中通过图标运行启动程序（或者是通过命令启动程序），比较将程序编译成 jar 包，然后通过命令启动程序他们之间是否相同，如果不同请解释为什么？

### 问题一

项目的应用启动入口可以不是 {% label success@main.class %} 方法，只是为什么会默认为 {% label success@main.class %} 方法，原因是在 springboot 的 MainMethodRunner类的 run 方法中，是固定写死的 {% label success@main %} ，为什么要这么写，答案是，我们可以在编辑器中已右键或其他图标启动的方式快速启动 springboot 项目（就像是在运行一个 Java 的 {% label success@main %} 方法一样，不再向之前需要乱七八糟各种的配置）。

### 问题二

答案是不相同，我们可以在项目的应用启动 {% label success@main.class %} 方法中，打印出加载类 {% label info@System.out.println("项目启动加载类" + SpringbootStartApplication.class.getClassLoader()); %} ，这样就可以检验我们的分析是否正确。分别使用两种不同的方式
* 方式一：在编辑器中之间运行（右键，或者控制台输入命令`gradle bootRun`）或者使用 IDEA 上的运行应用运行按钮，结果如下
    ```java
    项目启动加载类sun.misc.Launcher$AppClassLoader@18b4aac2
    ```
* 方式二：先编译成 jar 包，然后通过 {% label info@java -jar build-name.jar %} 命令运行
    ```java
    项目启动加载类org.springframework.boot.loader.LaunchedURLClassLoader@439f5b3d
    ```

通过打印出来的信息，可以验证我们的分析，方式一的运行，实际上是应用类加载器启动，而方式二是 {% label danger@spring-boot-load %} 包中自定义的 `LaunchedURLClassLoader` 来启动项目

在实际的生产开发中，有时我们的分析需要进行验证（或者找问题），而此时服务又部署在生成环境或者非本机上，通常用的方式是看应用的日志输出，在日志中去定位问题，而有时我们需要断点的方式去找问题，那该如何去操作呢？对于这个问题，在实际开发中是有方法去处理，请看下篇[《SpringBoot（三） JDWP远程调用》](https://incoder.org/2019/07/11/springboot3/)

## 附录

* [spring_boot_cloud(2)Spring_Boot打包文件结构深入分析源码讲解](https://ceaser.wang/2019/06/07/spring_boot_and_cloud/spring_boot_cloud(2)Spring_Boot%E6%89%93%E5%8C%85%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84%E6%B7%B1%E5%85%A5%E5%88%86%E6%9E%90%E6%BA%90%E7%A0%81%E8%AE%B2%E8%A7%A3/)
* [校验者•CeaserWang](https://ceaser.wang/)