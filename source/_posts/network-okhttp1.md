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
> 关于网络请求
基本网络请求由请求（`请求行`，`请求头`，`请求内容`），响应（`响应行`，`响应头`，`响应内容`）两大部分组成，具体的内容请查看[Http VS Https](https://incoder.org/2018/06/22/network-http)这篇文章

### OkHttp请求
已在[Http VS Https](https://incoder.org/2018/06/22/network-http/#%E8%AF%B7%E6%B1%82%E6%8A%A5%E6%96%87)文章中介绍了，HTTP请求相关内容

### OkHttp响应
已在[Http VS Https](https://incoder.org/2018/06/22/network-http/#%E5%93%8D%E5%BA%94%E6%8A%A5%E6%96%87)文章中介绍了，HTTP响应相关内容

## 同步与异步
网络请求执行方式为：同步与异步；`同步`和`异步`关注的是消息通信机制 (synchronous communication/ asynchronous communication)

### 同步
就是在发出一个 **调用** 时，在没有得到结果之前，该 **调用** 就不返回，但是一旦调用返回，就得到返回值了。  
换句话说，就是由 **调用者** 主动等待这个 **调用** 的结果。
Okhttp同步(`execute()`)：Invokes the request immediately, and blocks until the response can be processed or is in error.

```java
String url = "https://api.github.com/users/BladeCode";
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
String url = "https://api.github.com/users/BladeCode";
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

### Posting a String
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
 
    try(Response response = client.newCall(request).execute()){
        if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
        System.out.println(response.body().string());
    }
}
```
> 注意：当提交数据大于1MB，请使用流的方式

### Post Streaming
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
 
    try(Response response = client.newCall(request).execute()){
        if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
        System.out.println(response.body().string());
    }
}
```

### Posting a File
```java
public static final MediaType MEDIA_TYPE_MARKDOWN = MediaType.parse("text/x-markdown; charset=utf-8");
 
private final OkHttpClient client = new OkHttpClient();
 
public void run() throws Exception {
    File file = new File("README.md");
 
    Request request = new Request.Builder()
        .url("https://api.github.com/BladeCode/raw")
        .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, file))
        .build();
 
    try(Response response = client.newCall(request).execute()){
        if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
        System.out.println(response.body().string());
    }
}
```

### Posting form parameters
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

    try(Response response = client.newCall(request).execute()){
        if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
        System.out.println(response.body().string());
    }
}
```

### Posting a multipart request
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

    try(Response response = client.newCall(request).execute()){
        if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);
 
        System.out.println(response.body().string());
    }
}
```
## Header
通常，HTTP headers 的工作方式类似于 `Map<String, String>`：每个字段都有一个值或没有，但是一些headers允许多个值
* 例如：Guava's Multimap.
* 例如：提供多个vary headers的HTTP响应是合法且常见的。OkHttp的API试图使用两种情况都很舒适

在编写请求headers时
* 使用 `header(name, value)`将 `name` 的唯一内容设置为 `value`。如果 `name` 存在现有值，则在添加新值之前将删除它。
* 使用 `addHeader(name, value)` 添加 `headers` 不会删除已存在的 `header`

在读取headers响应时，使用 `header(name)` 返回最后异常出现的命名值。通常这也是唯一发生，如果没有值，则 `header(name)` 返回null。将所有字段的值作为列表读取，请使用 `headers(name)`

```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("https://api.github.com/repos/square/okhttp/issues")
        .header("User-Agent", "OkHttp Headers.java")
        .addHeader("Accept", "application/json; q=0.5")
        .addHeader("Accept", "application/vnd.github.v3+json")
        .build();

    try (Response response = client.newCall(request).execute()) {
      if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

      System.out.println("Server: " + response.header("Server"));
      System.out.println("Date: " + response.header("Date"));
      System.out.println("Vary: " + response.headers("Vary"));
    }
}
```

### Response Caching
实现缓存响应，你需要一个可以读写的缓存目录，以及缓存大小的限制。缓存目录应该是私有的，不受信任的应用程序不应该读取其内容

让多个缓存同时访问同一缓存目录是错误的。大多数应用程序应该只调用一次 `new OkHttpClient()`，使用它们的缓存配置它，并在任何地方使用相同的实例。否则，两个缓存实例将互相踩踏，破坏响应缓存，并可能导致程序奔溃

响应缓存使用HTTP headers进行所有的配置。你可以添加headers，如：`Cache-Control: max-stale=3600`，OkHttp的缓存将遵循它。你的Web服务器使用自己的响应headers配置缓存响应的时间，例如：`Cache-Control: max-age=9600`。有缓存headers可强制缓存响应，强制网络响应，或者强制使用条件GET验证网络响应

```java
private final OkHttpClient client;

public CacheResponse(File cacheDirectory) throws Exception {
    // 设置缓存大小 10 MiB
    int cacheSize = 10 * 1024 * 1024; 
    // 实例化Cache对象
    Cache cache = new Cache(cacheDirectory, cacheSize);

    client = new OkHttpClient.Builder()
        .cache(cache)
        .build();
}

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("http://publicobject.com/helloworld.txt")
        .build();

    String response1Body;
    try (Response response1 = client.newCall(request).execute()) {
      if (!response1.isSuccessful()) throw new IOException("Unexpected code " + response1);

      response1Body = response1.body().string();
      System.out.println("Response 1 response:          " + response1);
      System.out.println("Response 1 cache response:    " + response1.cacheResponse());
      System.out.println("Response 1 network response:  " + response1.networkResponse());
    }

    String response2Body;
    try (Response response2 = client.newCall(request).execute()) {
      if (!response2.isSuccessful()) throw new IOException("Unexpected code " + response2);

      response2Body = response2.body().string();
      System.out.println("Response 2 response:          " + response2);
      System.out.println("Response 2 cache response:    " + response2.cacheResponse());
      System.out.println("Response 2 network response:  " + response2.networkResponse());
    }

    System.out.println("Response 2 equals Response 1? " + response1Body.equals(response2Body));
}
```
* 要阻止响应使用缓存，请使用 `CacheControl.FORCE_NETWORK`
* 要阻止它使用网络，请使用 `CacheControl.FORCE_CACHE`

>警告：如果你使用 `FORCE_CACHE` 且响应需要网络，OkHttp将返回504不满意请求响应

### Canceling a Call
使用 `Call.cancel()` 立即停止正在进行的请求，如果线程当前正在请求或读取响应，则它将收到 `IOException`。当不在需要call时，使用它来保护网络，例如，当你的用户导航离开应用程序时，同步和异步调用都可以取消

```java
private final ScheduledExecutorService executor = Executors.newScheduledThreadPool(1);
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("http://httpbin.org/delay/2") // This URL is served with a 2 second delay.
        .build();

    final long startNanos = System.nanoTime();
    final Call call = client.newCall(request);

    // Schedule a job to cancel the call in 1 second.
    executor.schedule(new Runnable() {
      @Override public void run() {
        System.out.printf("%.2f Canceling call.%n", (System.nanoTime() - startNanos) / 1e9f);
        call.cancel();
        System.out.printf("%.2f Canceled call.%n", (System.nanoTime() - startNanos) / 1e9f);
      }
    }, 1, TimeUnit.SECONDS);

    System.out.printf("%.2f Executing call.%n", (System.nanoTime() - startNanos) / 1e9f);
    try (Response response = call.execute()) {
      System.out.printf("%.2f Call was expected to fail, but completed: %s%n",
          (System.nanoTime() - startNanos) / 1e9f, response);
    } catch (IOException e) {
      System.out.printf("%.2f Call failed as expected: %s%n",
          (System.nanoTime() - startNanos) / 1e9f, e);
    }
}
```

### Timeouts
当无法访问时，使用超时来使call失败。网络分区可能是由于客户端连接问题，服务器可读性问题或其他任何问题时。OkHttp支持连接，读取和写入超时配置

```java
private final OkHttpClient client;

public ConfigureTimeouts() throws Exception {
    client = new OkHttpClient.Builder()
        .connectTimeout(10, TimeUnit.SECONDS)
        .writeTimeout(10, TimeUnit.SECONDS)
        .readTimeout(30, TimeUnit.SECONDS)
        .build();
}

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("http://httpbin.org/delay/2") // This URL is served with a 2 second delay.
        .build();

    try (Response response = client.newCall(request).execute()) {
      System.out.println("Response completed: " + response);
    }
}
```

### Per-call Configuration
所有的HTTP 客户端配置都在 `OkHttpClient` 中，包括代理设置，超时和缓存。当你需要修改单个调用时的配置时，请调用 `OkHttpClient.newBuilder()`。这将返回与原始客户端共享相同连接池，调度程序和配置的构建器（`Builder`）

```java
// 示例：我们发出一个请求，其中500毫秒超时，另一个请求超时3000毫秒
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
    Request request = new Request.Builder()
        // This URL is served with a 1 second delay.
        .url("http://httpbin.org/delay/1") 
        .build();

    // Copy to customize OkHttp for this request.
    OkHttpClient client1 = client.newBuilder()
        .readTimeout(500, TimeUnit.MILLISECONDS)
        .build();
    try (Response response = client1.newCall(request).execute()) {
      System.out.println("Response 1 succeeded: " + response);
    } catch (IOException e) {
      System.out.println("Response 1 failed: " + e);
    }

    // Copy to customize OkHttp for this request.
    OkHttpClient client2 = client.newBuilder()
        .readTimeout(3000, TimeUnit.MILLISECONDS)
        .build();
    try (Response response = client2.newCall(request).execute()) {
      System.out.println("Response 2 succeeded: " + response);
    } catch (IOException e) {
      System.out.println("Response 2 failed: " + e);
    }
}
```

### Handling authentication
OkHttp可以自动重试未经身份验证的请求。如果响应为401 Not Authorized，则要求Authenticator提供凭证。实现应该构建一个包含缺少凭证的新请求。如果没有可用的凭证，则返回null以跳过重试。

使用 `Response.challenges()`来获取任何身份验证挑战的方案和领域。在完成基本挑战时，使用 `Credentials.basic(username, password)` 对请求header进行编码

```java
private final OkHttpClient client;

public Authenticate() {
    client = new OkHttpClient.Builder()
        .authenticator(new Authenticator() {
          @Override public Request authenticate(Route route, Response response) throws IOException {
            if (response.request().header("Authorization") != null) {
              return null; // Give up, we've already attempted to authenticate.
            }

            System.out.println("Authenticating for response: " + response);
            System.out.println("Challenges: " + response.challenges());
            String credential = Credentials.basic("jesse", "password1");
            return response.request().newBuilder()
                .header("Authorization", credential)
                .build();
        }
    })
        .build();
}

public void run() throws Exception {
    Request request = new Request.Builder()
        .url("http://publicobject.com/secrets/hellosecret.txt")
        .build();

    try (Response response = client.newCall(request).execute()) {
      if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

      System.out.println(response.body().string());
    }
}
```

为避免在身份验证不起作用时进行多次重试，你可以在返回null以放弃，例如，你可能希望在尝试这些确切凭证时跳过重试
```java
if (credential.equals(response.request().header("Authorization"))) {
    return null; // If we already failed with these credentials, don't retry.
}
```

当你达到应用程序定义的尝试限制时，你也可以跳过重试
```java
private int responseCount(Response response) {
    int result = 1;
    while ((response = response.priorResponse()) != null) {
      result++;
    }
    return result;
}

if (responseCount(response) >= 3) {
    // If we've failed 3 times, give up.
    return null; 
}
```

## 附录
* [OkHttp Wiki](https://github.com/square/okhttp/wiki)
* [怎样理解阻塞非阻塞与同步异步的区别](https://www.zhihu.com/question/19732473)
* [OkHttp使用教程](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0106/2275.html)