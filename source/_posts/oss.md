---
title: OSS 初体验
date: 2021-03-27 09:44:46
categories: OSS
tag: [OSS]
---

在之前 [SpringBoot（十二）文件上传](https://incoder.org/2021/03/10/springboot12) 文章中，已经学习了使用 SpringBoot 基础的功能，完成静态资源的管理，本片文章我们同样也是对非结构化的静态数据进行管理，不过这次我们使用的是比较常用的 OSS 服务，废话不说，我们一起开始 OSS 之旅吧

<!-- more -->

## 什么是 OSS

OSS 是一种面向海量数据规模的分布式存储服务，具有稳定，可靠，安全，低成本的特点。主要用来存储各种非结构化的数据，比如视频，图像，日志，文本文件等。OSS 服务提供标准的 RESTful API 接口，并提供一些常用语言的 SDK 包，方便开发者进行快速开发和二次处理

## 常用的 OSS

市面上提供云服务的厂商有很多，这里以阿里云的 OSS 服务为主来，完成 OSS 相关的学习和实践

## 依赖

```xml
<!-- https://mvnrepository.com/artifact/com.aliyun.oss/aliyun-sdk-oss -->
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>3.11.1</version>
</dependency>
```

## OSS 工具类

```java
/**
 * OSS 文件上传
 *
 * @author : Jerry xu
 * @since : 2020/11/3  09:12
 */
@Slf4j
public class OssUtils {

    // 访问域名
    private static final String ENDPOINT = "xxxxx";
    // 存储空间
    private static final String BUCKET_NAME = "xxxxx";
    //==================访问密钥==================
    private static final String ACCESS_KEY_ID = "xxxxx";
    // 用户用于加密签名字符串和OSS用来验证签名字符串的密钥，必须保密
    private static final String ACCESS_KEY_SECRET = "xxxxx";

    /**
     * 通过文件上传图片
     *
     * @param file 文件
     * @return 上传结果地址
     */
    public static String uploadFileByFile(File file) {
//        // NIO 方式
//        byte[] fileByte = Files.readAllBytes(new File(file.getPath()).toPath());
//        // 其他方式
//        byte[] fileByte = Files.readAllBytes(Paths.get(file.getPath()));
//        return uploadFileByByte(fileByte, file);
        URL url;
        String urlStr = null;
        String[] split = new String[0];
        try {
            OSS ossClient = new OSSClientBuilder().build(ENDPOINT, ACCESS_KEY_ID, ACCESS_KEY_SECRET);
            // 获取文件名
            String fileName = file.getName();
            ossClient.putObject(BUCKET_NAME, fileName, file);
            // 设置过期时间
            url = ossClient.generatePresignedUrl(BUCKET_NAME, fileName, new Date(System.currentTimeMillis() + 3600 * 24 * 365 * 10));
            log.info("原始图片地址：{}", url);
            urlStr = url.toString();
//            byte[] fileByte = Files.readAllBytes(new File(file.getPath()).toPath());
//            PutObjectRequest putObjectRequest = new PutObjectRequest(BUCKET_NAME, "test", new ByteArrayInputStream(fileByte));
//            // 不设置过期时间
//            PutObjectResult putObjectResult = ossClient.putObject(putObjectRequest);
//            // 去除过期时间参数地址
//            split = urlStr.split("\\?");
        } catch (Exception e) {
            log.warn(e.getMessage());
        }
        return urlStr;
    }

    /**
     * 通过字节数组上传图片
     *
     * @param binaryBytes 字节数组
     * @param fileName    文件名
     * @return 上传结果地址
     */
    public static String uploadFileByByte(byte[] binaryBytes, String fileName) {
        InputStream inputStream = new ByteArrayInputStream(binaryBytes);
        return uploadFileByInputStream(inputStream, fileName);
    }

    /**
     * 通过输入流上传图片
     *
     * @param inputStream 输入流
     * @param fileName    文件名
     * @return 上传结果地址
     */
    public static String uploadFileByInputStream(InputStream inputStream, String fileName) {
        URL url;
        String urlStr = null;
        String[] split = new String[0];
        try {
            OSS ossClient = new OSSClientBuilder().build(ENDPOINT, ACCESS_KEY_ID, ACCESS_KEY_SECRET);
//            String fileName = UUID.randomUUID().toString() + ".jpeg";
            ossClient.putObject(BUCKET_NAME, fileName, inputStream);
            // 设置过期时间
            url = ossClient.generatePresignedUrl(BUCKET_NAME, fileName, new Date(System.currentTimeMillis() + 3600 * 24 * 365 * 10));
            log.info("原始图片地址：{}", url);
            urlStr = url.toString();
//            byte[] fileByte = Files.readAllBytes(new File(file.getPath()).toPath());
//            PutObjectRequest putObjectRequest = new PutObjectRequest(BUCKET_NAME, "test", new ByteArrayInputStream(fileByte));
//            // 不设置过期时间
//            PutObjectResult putObjectResult = ossClient.putObject(putObjectRequest);
//            // 去除过期时间参数地址
//            split = urlStr.split("\\?");
        } catch (Exception e) {
            log.warn(e.getMessage());
        }
        return urlStr;
    }

}
```

## 上传

这里我们写一个上传接口

```java
@ApiOperation("文件上传", notes = "支持多图上传")
@PostMapping(value = "/upload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public List<String> uploadTest(@RequestParam("file") List<MultipartFile> file) {
    List<String> uploadList = new ArrayList<>(file.size());
    file.forEach(t -> {
        try {
            String url = OssUtils.uploadFileByInputStream(t.getInputStream(), t.getOriginalFilename());
            uploadList.add(url);
        } catch (IOException e) {
            e.printStackTrace();
        }
    });
    return uploadList;
}
```

## 测试

不废话了，直接看图就好了
![](https://res.cloudinary.com/incoder/image/upload/v1618848130/blog/oss-upload.png)

## 问题

1. 对于上传获取到的文件地址是一个会过期的地址，并不是一个固定不变的地址，如上截图所示，我偷懒直接将地址链接出的相关参数删去，拿到了一个永久存储的访问连接地址。但这里需要注意，这需要在你的 OSS 管理后台去设置你的文件存储的过期策略。这里就不进行截图演示了（主要是我没有登录系统的账号密码，逃 ~）
2. 对于上传的文件我没有自定义文件名，这里有个问题是当用户上传 OSS 服务中已经存在的文件名的文件时，新上传的会覆盖旧文件，因此这个地方需要根据实际的业务场景选择合适的方式。在 <mark>OssUtils</mark> 工具类中我已经注释掉了将文件名重命名的代码，你可以在此处按照你的业务进行更改
3. 第三个问题就是结合上面的两点的汇总方案，其实呢，对于一般的系统，这些静态资源就存永久的连接地址即可。但目前新的系统对用户的资料等也有了 "稍微" 高一点的保护，就是这些资源都是有时效性的，获取的地址就是我们上传拿到的原始地址，而我们存放在数据库中当然也不会是之前那种永久的连接地址，而是对应图片的一个唯一标识信息（可以是重命名后的文件名或者其他能够唯一标识资源你的字段），然后用户访问这些资源时，用存放在数据库中的唯一标识去 OSS 服务上查询对应的资源，然后加载这个地址去显示。

## 参考

1. [阿里云对象存储 OSS](https://help.aliyun.com/product/31815.html)
2. [对象存储 Kodo](https://www.qiniu.com/products/kodo)
3. [华为云对象存储服务 OBS](https://support.huaweicloud.com/obs/index.html)