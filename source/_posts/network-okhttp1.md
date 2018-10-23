---
title: Network（一） 之OkHttp 入门
date: 2018-06-23 12:44:25
categories: Network
tag: OkHttp
---

自从Android4.4的源码中可以看到`HttpURLConnection`已经替换成`OkHttp`开始( [JakeWharton曾在Twitter表示](https://twitter.com/JakeWharton/status/482563299511250944) ) ，`OkHttp`+`Retrofit`+`RxJava`的组合网络请求一直经久不衰，主流app的网络架构基本都是这样的组合模式，存在即合理，说明`OkHttp`+`Retrofit`+`RxJava`的方式确实给开发，用户体验等带来可观的优势，那么这个系列文章围绕Android的网络展开.

OkHttp：An HTTP & HTTP/2 client for Android and Java applications

>Android 历史网络库
* `HttpClient` 是 Apache 提供的HTTP网络访问接口，从一开始的时候就被引入到了Android的API中；
* `HttpURLConnection` 是一种多用途, 轻量极的HTTP客户端, 提供的API比较简单, 可以容易地去使用和扩展. 

## OkHttp优势
* 支持HTTP/2, HTTP/2通过使用多路复用技术在一个单独的TCP连接上支持并发, 通过在一个连接上一次性发送多个请求来发送或接收数据
* 如果HTTP/2不可用, 连接池复用技术也可以极大减少延时
* 支持GZIP, 可以压缩下载体积
* 响应缓存可以直接避免重复请求
* 会从很多常用的连接问题中自动恢复
* 如果您的服务器配置了多个IP地址, 当第一个IP连接失败的时候, OkHttp会自动尝试下一个IP
* OkHttp还处理了代理服务器问题和SSL握手失败问题，等等...

## 基本使用
该系列版本说明  
* OkHttp版本统一：**3.10.0**
* JDK：**1.8+**

Gradle包导入
```groovy
// okhttp核心库
implementation 'com.squareup.okhttp3:okhttp:3.10.0'
// okhttp网络请求拦截日志库
implementation 'com.squareup.okhttp3:logging-interceptor:3.10.0'
```
### OkHttp请求
已在[Http VS Https](https://incoder.org/2018/06/22/network-http/#%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87)文章中介绍了，HTTP请求相关内容

### OkHttp响应
已在[Http VS Https](https://incoder.org/2018/06/22/network-http/#%E5%93%8D%E5%BA%94%E6%8A%A5%E6%96%87)文章中介绍了，HTTP响应相关内容

## 同步与异步
`同步`和`异步`关注的是消息通信机制 (synchronous communication/ asynchronous communication)
### 同步
就是在发出一个 **调用** 时，在没有得到结果之前，该 **调用** 就不返回，但是一旦调用返回，就得到返回值了。  
换句话说，就是由 **调用者** 主动等待这个 **调用** 的结果。
Okhttp同步(`execute()`)：Invokes the request immediately, and blocks until the response can be processed or is in error.

```java
OkHttpClient client = new OkHttpClient();
 
String run(String url) throws IOException {
    Request request = new Request.Builder().url(url).build();
    // 执行同步操作
    Response response = client.newCall(request).execute();   
    if (response.isSuccessful()) {
          return response.body().string();
    } else {
          throw new IOException("Unexpected code " + response);
    }
}
```
### 异步
**异步** 则与同步相反，**调用** 在发出之后，这个调用就直接返回了，所以没有返回结果。  
换句话说，当一个异步过程调用发出后，**调用者** 不会立刻得到结果。而是在 **调用** 发出后，**被调用者** 通过状态、通知来通知 **调用者**，或通过回调函数处理这个调用。
Okhttp同步(`enqueue(Callback responseCallback)`)：Schedules the request to be executed at some point in the future.

```java
String url = "https://api.github.com/users/BladeCode";
OkHttpClient client = new OkHttpClient();

Request request = new Request.Builder().url(url).build();
// 返回response 对象
Response response = client.newCall(request).enqueue(new Callback() {

    @Override
    public void onFailure(Call call, IOException e) {
        System.out.println(e.toString());
    }

    @Override
    public void onResponse(Call call, Response response) throws IOException {
        // 字符串形式表达响应
        System.out.println(response.body().string());
        // 或流的形式表达响应
        System.out.println(response.body().charStream());
        System.out.println(response.body().byteStream());
    }
});
```
>注意：
* 响应体太大（超过1MB）, 应避免使用 string()方法, 因为它会将把整个文档加载到内存中.
* 对于超过1MB的响应body, 应使用流的方式来处理响应body. 这和我们处理xml文档的逻辑是一致的, 小文件可以载入内存树状解析, 大文件就必须流式解析

## OkHttp Get
```java
OkHttpClient client = new OkHttpClient();
 
String run(String url) throws IOException {
    Request request = new Request.Builder().url(url).build();
    Response response = client.newCall(request).execute();

    if (response.isSuccessful()) {        
        return response.body().string();
    } else {
        throw new IOException("Unexpected code " + response);
    }
}
```

## OkHttp Post
```java
public static final MediaType JSON = MediaType.parse("application/json; charset=utf-8");

OkHttpClient client = new OkHttpClient();

String post(String url, String json) throws IOException {
    RequestBody body = RequestBody.create(JSON, json);
    Request request = new Request.Builder()
      .url(url)
      .post(body)
      .build();
    
    Response response = client.newCall(request).execute();

    if (response.isSuccessful()) {
        return response.body().string();
    } else {
        throw new IOException("Unexpected code " + response);
    }

}
```

### 提交String
```java
public static final MediaType MEDIA_TYPE_MARKDOWN = MediaType.parse("text/x-markdown; charset=utf-8");
 
private final OkHttpClient client = new OkHttpClient();
 
public void run() throws Exception {
    String postBody = ""
        + "Releases\n"
        + "--------\n"
        + "\n"
        + " * _1.0_ May 6, 2013\n"
        + " * _1.1_ June 15, 2013\n"
        + " * _1.2_ August 11, 2013\n";
 
    Request request = new Request.Builder()
        .url("https://api.github.com/markdown/raw")
        .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, postBody))
        .build();
 
    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
    System.out.println(response.body().string());
}
```
> 注意：当提交数据大于1MB，请使用流的方式

### 提交流
```java
public static final MediaType MEDIA_TYPE_MARKDOWN = MediaType.parse("text/x-markdown; charset=utf-8");
 
private final OkHttpClient client = new OkHttpClient();
 
public void run() throws Exception {
    RequestBody requestBody = new RequestBody() {
      @Override 
      public MediaType contentType() {
        return MEDIA_TYPE_MARKDOWN;
      }
 
      @Override 
      public void writeTo(BufferedSink sink) throws IOException {
        sink.writeUtf8("Numbers\n");
        sink.writeUtf8("-------\n");
        for (int i = 2; i <= 997; i++) {
          sink.writeUtf8(String.format(" * %s = %s\n", i, factor(i)));
        }
      }
 
      private String factor(int n) {
        for (int i = 2; i < n; i++) {
          int x = n / i;
          if (x * i == n) return factor(x) + " × " + i;
        }
        return Integer.toString(n);
      }
    };
 
    Request request = new Request.Builder()
        .url("https://api.github.com/markdown/raw")
        .post(requestBody)
        .build();
 
    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
    System.out.println(response.body().string());
}
```

### 提交文件
```java
public static final MediaType MEDIA_TYPE_MARKDOWN = MediaType.parse("text/x-markdown; charset=utf-8");
 
private final OkHttpClient client = new OkHttpClient();
 
public void run() throws Exception {
    File file = new File("README.md");
 
    Request request = new Request.Builder()
        .url("https://api.github.com/BladeCode/raw")
        .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, file))
        .build();
 
    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
    System.out.println(response.body().string());
}
```

### 提交表单
使用`FormEncodingBuilder`来构建和HTML<form>标签相同效果的请求体。键值对将使用一种HTML兼容形式的URL编码来进行编码

```java
private final OkHttpClient client = new OkHttpClient();
 
public void run() throws Exception {
    RequestBody formBody = new FormEncodingBuilder()
        .add("search", "Jurassic Park")
        .build();
    Request request = new Request.Builder()
        .url("https://en.wikipedia.org/w/index.php")
        .post(formBody)
        .build();

    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
    System.out.println(response.body().string());
}
```

### 提交分块请求
`MultipartBuilder`可以构建复杂的请求体，与HTML文件上传形式兼容。多块请求体中每块请求都是一个请求体，可以定义自己的请求头。这些请求头可以用来描述这块请求，例如他的`Content-Disposition`。如果`Content-Length`和`Content-Type`可用的话，他们会被自动添加到请求头中。

```java
private static final String IMGUR_CLIENT_ID = "...";
private static final MediaType MEDIA_TYPE_PNG = MediaType.parse("image/png");

private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
    // Use the imgur image upload API as documented at https://api.imgur.com/endpoints/image
    RequestBody requestBody = new MultipartBuilder()
        .type(MultipartBuilder.FORM)
        .addPart(
            Headers.of("Content-Disposition", "form-data; name=\"title\""),
            RequestBody.create(null, "Square Logo"))
        .addPart(
            Headers.of("Content-Disposition", "form-data; name=\"image\""),
            RequestBody.create(MEDIA_TYPE_PNG, new File("website/static/logo-square.png")))
        .build();
 
    Request request = new Request.Builder()
        .header("Authorization", "Client-ID " + IMGUR_CLIENT_ID)
        .url("https://api.imgur.com/3/image")
        .post(requestBody)
        .build();

    Response response = client.newCall(request).execute();
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());
}
```

## 附录
* [怎样理解阻塞非阻塞与同步异步的区别](https://www.zhihu.com/question/19732473)
* [OkHttp使用教程](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0106/2275.html)