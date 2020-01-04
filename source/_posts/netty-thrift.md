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

###  下载Thrift

这一步可以直接通过 maven 或者 gradle 的方式集成 Thrift 包到所需要的项目包管理中即可

```xml
# maven
<dependency>∏
  <groupId>org.apache.thrift</groupId>
  <artifactId>libthrift</artifactId>
  <version>0.13.0</version>
</dependency>

# gradle
compile 'org.apache.thrift:libthrift:0.13.0'
```

### 编译器安装

对于 macOS 可使用官方提供的方式去安装，也可以借助于 macOS 上，优秀的包管理工具 [Homebrew](https://brew.sh) 来进行安装，我这里就直接使用 Homebrew 进行安装，其他系统可参考[官方文档](http://thrift.apache.org/docs/install)

```brew
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
/**
 * Ahh, now onto the cool part, defining a service. Services just need a name
 * and can optionally inherit from another service using the extends keyword.
 */
service Calculator extends shared.SharedService {

  /**
   * A method definition looks like C code. It has a return type, arguments,
   * and optionally a list of exceptions that it may throw. Note that argument
   * lists and exception lists are specified using the exact same syntax as
   * field lists in struct or exception definitions.
   */

   void ping(),

   i32 add(1:i32 num1, 2:i32 num2),

   i32 calculate(1:i32 logid, 2:Work w) throws (1:InvalidOperation ouch),

   /**
    * This method has a oneway modifier. That means the client only makes
    * a request and does not listen for any response at all. Oneway methods
    * must be void.
{% endcode %}

<!-- endtab -->

<!-- tab Python Client -->
Python Client

{% code %}
def main():
    # Make socket
    transport = TSocket.TSocket('localhost', 9090)

    # Buffering is critical. Raw sockets are very slow
    transport = TTransport.TBufferedTransport(transport)

    # Wrap in a protocol
    protocol = TBinaryProtocol.TBinaryProtocol(transport)

    # Create a client to use the protocol encoder
    client = Calculator.Client(protocol)

    # Connect!
    transport.open()

    client.ping()
    print('ping()')

    sum_ = client.add(1, 1)
{% endcode %}

<!-- endtab -->

<!-- tab Java Server -->
Java Server
{% code %}
    try {
      TServerTransport serverTransport = new TServerSocket(9090);
      TServer server = new TSimpleServer(new Args(serverTransport).processor(processor));

      // Use this for a multithreaded server
      // TServer server = new TThreadPoolServer(new TThreadPoolServer.Args(serverTransport).processor(processor));

      System.out.println("Starting the simple server...");
      server.serve();
    } catch (Exception e) {
      e.printStackTrace();
    }
{% endcode %}

<!-- endtab -->

<!-- tab Java Client -->
Java Client

{% code %}
public class CalculatorHandler implements Calculator.Iface {

  private HashMap<Integer,SharedStruct> log;

  public CalculatorHandler() {
    log = new HashMap<Integer, SharedStruct>();
  }

  public void ping() {
    System.out.println("ping()");
  }

  public int add(int n1, int n2) {
    System.out.println("add(" + n1 + "," + n2 + ")");
    return n1 + n2;
  }

  public int calculate(int logid, Work work) throws InvalidOperation {
    System.out.println("calculate(" + logid + ", {" + work.op + "," + work.num1 + "," + work.num2 + "})");
    int val = 0;
    switch (work.op) {
    case ADD:
      val = work.num1 + work.num2;
      break;
    case SUBTRACT:
      val = work.num1 - work.num2;
      break;
    case MULTIPLY:
      val = work.num1 * work.num2;
      break;
    case DIVIDE:
      if (work.num2 == 0) {
        InvalidOperation io = new InvalidOperation();
        io.whatOp = work.op.getValue();
        io.why = "Cannot divide by 0";
        throw io;
      }
      val = work.num1 / work.num2;
      break;
    default:
      InvalidOperation io = new InvalidOperation();
      io.whatOp = work.op.getValue();
      io.why = "Unknown operation";
      throw io;
    }

    SharedStruct entry = new SharedStruct();
    entry.key = logid;
    entry.value = Integer.toString(val);
    log.put(logid, entry);

    return val;
  }

  public SharedStruct getStruct(int key) {
    System.out.println("getStruct(" + key + ")");
    return log.get(key);
  }

  public void zip() {
    System.out.println("zip()");
  }

}
{% endcode %}

<!-- endtab -->

{% endtabs %}