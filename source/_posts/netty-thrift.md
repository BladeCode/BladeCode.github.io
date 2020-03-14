---
title: Netty（三）之 Thrift
date: 2019-12-01 08:03:10
categories: Netty
tag: [Netty, Thrift]
---

The Apache Thrift software framework, for scalable cross-language services development, combines a software stack with a code generation engine to build services that work efficiently and seamlessly between C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, OCaml and Delphi and other languages.

Apache Thrift软件框架，用于可扩展的跨语言服务开发，它包含软件栈和一个代码生成器用于构建服务，这个服务可以高效并且无缝的在 C++，Java，Python，PHP，Ruby，Erlang，Perl，Haskell，C#，Cocoa，Node.js，Smalltalk，OCaml 和 Delphi 等其他语言间协作

<!-- more -->

[Apache Thrift](http://thrift.apache.org) 与 [Google Protocal Buffers](https://developers.google.com/protocol-buffers) 都是一种可以用于在 Netty 之上的一种数据格式，Thrift 可应用的语言比 Protocal Buffers 多，并且 Thrift 除了用于传递数据的定义，底层还提供了传输层，因此可以单独的去使用，而不必强制运行在 Netty 载体之上

## Thrift

### Thrift数据类型

Thrift不支持无符号类型，因为很多编程语言不存在无符号类型，比如Java

* byte：有符号字节
* i16：16位有符号整数
* i32：32位有符号整数
* i64：64位有符号整数
* double：64位浮点数
* string：字符串
* bool：布尔值

### Thrift容器类型

* list：一系列由T类型的数据组成有序列表，元素可以重复
  >集合中的元素可以是除了service之外的任何类型，包括exception
* set：一系列由T类型的数据组成的无序列表，元素不可以重复
* map：一个字典结构，key 为 K类型，value为 V类型，相当于Java中的 HashMap

### Thrift支持的三类组件

#### struct

结构体，编译生成完成后，对应的是我们的类，就像 C 语言一样，thrift 支持 struct 类型，目的就是将一些数据聚合在一起，方便传输管理。struct 的定义形式如下：

```java
struct People{
  1:string name;
  2:i32 age;
  3:string gender;
}
```

枚举，枚举的定义形式和 Java 的 Enum 定义类似
```java
enum Gender{
  MALE,
  FEMALE
}
```

#### exception

异常，客户端与服务端之间通信用到的接口可能抛出的异常，thrift 支持自定义 exception，规则与 struct 一样
```java
exception RequestException{
  1:i32 code;
  2:string reason;
}
```

#### service

服务，客户端与服务端之间通信用到的接口，thrift 定义服务相当于 Java 中创建 interface 一样，创建的 service 经过代码生成命令之后就会生成客户端和服务端的框架代码
```java
service HelloWorldService{
  // service 中定义的函数，相当于 Java interface 中定义的方法
  string doAction(1:string name, 2:i32 age);
}
```

### 类型定义

thrift 支持类似 C++ 一样的 typedef 定义，在定义完别名后，在后面的 IDL 文件中就可以使用别名进行编写
```c++
// 把 i32 别名成 int
typedef i32 int
// 把 i64 别名成 long
typedef i64 long
```

### 常量

thrift 也支持常量定义，使用 const 关键字
```java
const i32 MAX_RETRIES_TIME = 10
const string MY_WEBSITE = "https://incoder.org"
```

### 命名空间

thrift 的命名空间相当于 Java 中的 package 的意思，主要目的是组织代码。thrift 使用关键字 namespace 定义命名空间

```java
// 格式：namespace 语言名 路径
namespace java org.incoder.thrift
```

### 文件包含

thrift 也支持文件包含，相当于 C/C++ 中的 include，Java 中的 import，使用关键字 include 定义
```C
include "global.thrift"
```

### 可选与必选

thrift 提供两个关键字`required`，`optional`，分别用于表示对应的字段是必填还是可选，主要根据你的业务来选择，推荐使用 optional
```java
1:required string name;
2:optional i32 age;
```

### Thrift工作原理

数据之间的传输使用socket（多种语言均支持），数据载以特定的格式（String等）发送，接收方进行语言解析。通过定义 Thrift 文件，由 Thrift 文件（IDL）生成双方语言的接口，model，在生成的model及接口中会有解析码，编码的代码

## Thrift 实践

### 下载Thrift

这一步可以直接通过 maven 或者 gradle 的方式集成 Thrift 包到所需要的项目包管理中即可

```xml
# maven
<dependency>
  <groupId>org.apache.thrift</groupId>
  <artifactId>libthrift</artifactId>
  <version>0.13.0</version>
</dependency>

# gradle
compile 'org.apache.thrift:libthrift:0.13.0'
```

### 编译器安装

对于 macOS 可使用官方提供的方式去安装，也可以借助于 macOS 上，优秀的包管理工具 [Homebrew](https://brew.sh) 来进行安装，我这里就直接使用 Homebrew 进行安装，其他系统可参考[官方文档](http://thrift.apache.org/docs/install)

```bash
brew install thrift
```

### 编写.thrift文件

编写.thrift 文件的指南，可以参考[官网文档](http://thrift.apache.org/docs/idl)，编写完文件，使用 thrift 编译器提供的命令生成相关的代码

```bash
thrift --gen <language> <Thrift filename>
```

## 示例

{% tabs Tags %}

<!-- tab Thrift Definition File -->
Thrift Definition File
{% code %}
// 定义命名空间
namespace java org.incoder.thrift.java
namespace py org.incoder.thrift.py

// 定义别名
typedef i16 short
typedef i32 int
typedef i64 long
typedef bool boolean
typedef string String

// 定义 struct
struct Person{
    1: optional String username,
    2: optional int age,
    3: optional boolean married
}

// 定义 exception
exception DataException{
    1: optional String message,
    2: optional String callStack,
    3: optional String date
}

// 定义 service
service PersonService{
    Person getPersonByUsername(1: required String username) throws (1: DataException dataException),

    void savePerson(1: required Person person) throws (1: DataException dataException)
}
{% endcode %}
<!-- endtab -->

<!-- tab Python Service -->
{% code lang:py %}
try:
    person_handler = PersonHandler()
    processor = PersonService.Processor(person_handler)

    serverSocket = TSocket.TServerSocket(port=9090)
    transportFactory = TTransport.TFramedTransportFactory()
    protocolFactory = TCompactProtocol.TCompactProtocolFactory()

    server = TServer.TSimpleServer(processor, serverSocket, transportFactory, protocolFactory)
    server.serve()

except Thrift.TException as tx:
    print(tx.message)
{% endcode %}
<!-- endtab -->

<!-- tab Python Client -->
{% code lang:py %}
try:
    tSocket = TSocket.TSocket('localhost', 9090)
    tSocket.setTimeout(600)

    transport = TTransport.TFramedTransport(tSocket)
    protocol = TCompactProtocol.TCompactProtocol(transport)
    client = PersonService.Client(protocol)

    transport.open()
    person = client.getPersonByUsername("张三")
    print("username：" + person.username)
    print("age：" + str(person.age))
    print("married：" + str(person.married))

    print("------------------------")
    newPerson = ttypes.Person()
    newPerson.username = "李四"
    newPerson.age = 30
    newPerson.married = True

    client.savePerson(newPerson)
    transport.close()
except Thrift.TException as tx:
    print(tx.message)
{% endcode %}
<!-- endtab -->

<!-- tab Java Server -->
Java Server
{% code lang:java %}
public static void main(String[] args) throws Exception {
    TNonblockingServerSocket serverSocket = new TNonblockingServerSocket(9090);
    THsHaServer.Args arg = new THsHaServer.Args(serverSocket).minWorkerThreads(2).maxWorkerThreads(4);
    PersonService.Processor<PersonServiceImpl> processor = new PersonService.Processor<>(new PersonServiceImpl());

    arg.protocolFactory(new TCompactProtocol.Factory());
    arg.transportFactory(new TFramedTransport.Factory());
    arg.processorFactory(new TProcessorFactory(processor));

    TServer server = new THsHaServer(arg);
    System.out.println("Thrift service Started!");
    // 开启死循环
    server.serve();
}
{% endcode %}
<!-- endtab -->

<!-- tab Java Client -->
{% code lang:java %}
public static void main(String[] args) {
    TTransport transport = new TFramedTransport(new TSocket("localhost", 9090), 600);
    TProtocol protocol = new TCompactProtocol(transport);
    PersonService.Client client = new PersonService.Client(protocol);

    try {
        transport.open();
        // 调用定义通过用户名获取用户信息的接口方法 getPersonByUsername
        Person person = client.getPersonByUsername("张三");
        System.out.println(person.getUsername());
        System.out.println(person.getAge());
        System.out.println(person.isMarried());

        System.out.println("-----------------------------");

        // 调用定义保存用户信息的方法
        Person per = new Person();
        per.setUsername("李四");
        per.setAge(30);
        per.setMarried(true);
        client.savePerson(per);
    } catch (Exception e) {
        throw new RuntimeException(e.getMessage(), e);
    } finally {
        transport.close();
    }
}
{% endcode %}
<!-- endtab -->

{% endtabs %}

>相关源码[rc-cluster-netty](https://github.com/RootCluster/rc-cluster-netty/tree/master/src/main/java/org/incoder/thrift)

## 其他

本地 Thrift 的 Python 环境，需要下载官方的文件，进行安装

```bash
# 方式一：
pip3 install thrift
# 方式二，下载官方包，进行安装
# 1. 下载文件
curl https://mirrors.tuna.tsinghua.edu.cn/apache/thrift/0.13.0/thrift-0.13.0.tar.gz
# 2. 解压文件
tar -zvxf thrift-0.13.0.tar.gz
# 3. 安装thrift
cd thrift-0.13.0/lib/py/
sudo python setup.py install
```