---
title: Realm 数据库快速上手
date: 2018-04-24 01:11:10
categories: DataBase
tag: Realm
---

![realm-db](https://res.cloudinary.com/incoder/image/upload/v1525517554/blog/gitpages-realm-mobile-db.png)

<!-- more -->

Android 供了多种选项来保存永久性应用数据。
* [Shared preferences](https://developer.android.google.cn/guide/topics/data/data-storage.html?hl=zh-cn#pref)
* [Internal file storage](https://developer.android.google.cn/guide/topics/data/data-storage.html?hl=zh-cn#filesInternal)
* [External file storage](https://developer.android.google.cn/guide/topics/data/data-storage.html?hl=zh-cn#filesExternal)
* [Databases](https://developer.android.google.cn/guide/topics/data/data-storage.html?hl=zh-cn#db)
* [Network](https://developer.android.google.cn/guide/topics/data/data-storage.html?hl=zh-cn#netw)

其中数据库存储是一种必备技能，而衍生的mobile db也是层出不穷，本节主要介绍全平台(除Android,iOS,macOS外还支持web,桌面应用)[Realm](https://realm.io)数据库在Android上的使用

## 快速上手

* [Android Studio 1.5.1+](https://developer.android.google.cn/studio/index.html?hl=zh-cn)
* JDK1.7+
* Android API 9+
* Realm 默认情况下使用内部存储（internal storage)，一般来说，这个文件位于`/data/data/<packagename>/files/`，文件名：`default.realm`

### 集成

* 在项目的 build.gradle 文件中添加如下 class path 依赖
    ```groovy
    buildscript {
        repositories {
            jcenter()
        }
        dependencies {
            classpath "io.realm:realm-gradle-plugin:5.0.0"
        }
    }
    ```
* 在 app 的 build.gradle 文件中应用 realm-android 插件
    ```groovy
    apply plugin: 'realm-android'
    ```

### 初始化
* 默认初始化
    ```java
    public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        // 默认Realm的配置文件
        Realm.init(this);
    }
    }
    ```
* 自定义初始化
    ```java
    public class MyApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        // 自定义配置Realm
        initRealm();
    }

    private void initRealm() {
        RealmConfiguration config = new RealmConfiguration.Builder()
            .name("myrealm.realm")          // 命名文件名：myrealm.realm
            .inMemory()                     // 一个非持久化的、存在于内存中的 Realm 实例
            .encryptionKey(getKey())        // 数据库加密key
            .schemaVersion(2)               // 数据库结构版本号
            .modules(new MySchemaModule())  // 数据库结构对象
            .migration(new MyMigration())   // 数据库迁移
            .build();
        Realm.setDefaultConfiguration(config);
    }
    }
    ```
    >1. Realm 实例是线程单例化的，也就是说多次在同一线程调用静态构建器会返回同一 Realm 实例
    >2. 使用同样的名称同时创建“内存中的”Realm 和常规的（持久化）Realm 是不允许的

### 字段类型
Realm 支持以下字段类型：`boolean`、`byte`、`short`、`int`、`long`、`float`、`double`、`String`、`Date`和`byte []`。整数类型 `short`、`int` 和 `long` 都被映射到 Realm 内的相同类型（实际上为 `long` ）。

* @Required修饰类型和空值（null）
    >Realm强制禁止空值（null）被存储
    只有`Boolean`,`Byte`,`Short`,`Integer`,`Long`,`Float`,`Double`,`String`,`byte[]`,`Date`可被修饰
* @Ignore标识一个字段不应该被保存到 Realm
* @Index为字段增加搜索索引
    > 仅支持索引的属性类型包括：`String`,`byte`,`short`,`int`,`long`,`boolean`和`Date`
* @PrimaryKey
    > 必须为字符串（`String`）或整数（`short`,`int`,`long`）以及它们的包装类型（`Short`,`Int`,`Long`）

### 声明Realm数据模型

#### RealmObject
可以把RealmObject 当作POJO使用

```java
public class User extends RealmObject {

}
```

#### RealmModel
```java
@RealmClass
public class User implements RealmModel {

}
```

### 关系

#### 多对一
```java
public class Contact extends RealmObject {
    private Email email;
    // Other fields…
}

public class Email extends RealmObject {
    private String address;
    private boolean active;
    // ... setters and getters left out
}
```

#### 多对多

```java
public class Contact extends RealmObject {
    public String name;
    public RealmList<Email> emails;
}

public class Email extends RealmObject {
    public String address;
    public boolean active;
}
```

### CRUD
* 所有的写操作（添加、修改和删除对象），必须包含在写入事务（transaction）中  
* 在提交期间，所有更改都将被写入磁盘，并且，只有当所有更改可以被持久化时，提交才会成功。通过取消一个写入事务，所有更改将被丢弃。
* 益于 Realm 的 MVCC 架构，当正在进行一个写入事务时读取操作并不会被阻塞！这意味着，除非你需要从多个线程进行并发写入操作，否则，你可以尽量使用更大的写入事务来做更多的事情而不是使用多个更小的写入事务。

#### 增

* 事务执行
    ```java
    Realm realm = Realm.getDefaultInstance();
    realm.executeTransaction(new Realm.Transaction() {
        @Override
        public void execute(Realm realm) {
            User user = realm.createObject(User.class);
            user.setName("John");
            user.setEmail("john@corporation.com");
        }
    });
    ```
* 异步事务
    ```java
    Realm realm = Realm.getDefaultInstance();
    realm.executeTransactionAsync(new Realm.Transaction() {
        @Override
        public void execute(Realm bgRealm) {
            User user = bgRealm.createObject(User.class);
            user.setName("John");
            user.setEmail("john@corporation.com");
        }
    }, new Realm.Transaction.OnSuccess() {
        @Override
        public void onSuccess() {
            // Transaction was a success.
        }
    }, new Realm.Transaction.OnError() {
        @Override
        public void onError(Throwable error) {
            // Transaction failed and was automatically canceled.
        }
    });
    ```

    >OnSuccess 和 OnError 并不是必须重载的，重载了的回调函数会在事务成功或者失败时在被调用发生的线程执行。
#### 删

#### 改

#### 查

## Realm进阶

## Realm云
