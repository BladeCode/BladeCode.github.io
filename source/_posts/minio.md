---
title: OSS 之 Minio 初体验
date: 2021-03-16 15:30:10
categories: Linux
tag: [FileUpdate]
---

MinIO 是一个基于 Apache License v2.0 开源协议使用 Go 语言开发的对象存储服务。它兼容亚马逊 S3 云存储服务接口，非常适合于存储大容量非结构化的数据，例如图片、视频、日志文件、备份数据和容器/虚拟机镜像等，而一个对象文件可以是任意大小，从几 kb 到最大 5T 不等。MinIO 是一个非常轻量的服务,可以很简单的和其他应用的结合，类似 NodeJS, Redis 或者 MySQL。

<!-- more -->

MinIO 包含 MinIO Server, MinIO Client 以及方便开发基于不同编程语言使用的 MinIO SDK，这三部分组成，使用步骤也很简单，在服务器上安装 MinIO Server 应用，在项目中集成对应的 MinIO SDK，然后按照你的业务情况编写相应的实现即可，在开始前，我们先看看为什么我选择 MiniIO 作为自建的 OSS 服务

1. MinIO 由良好的存储机制
2. 兼容 Amason 的 S3 分布式存储
3. 天然的支持云原生
4. <mark>支持私有部署</mark>，可分布式，可单机，100%开源
5. 友好简单的部署方式，提供管理页面
6. 还可以配合其他的健康管理工具进行监控，比如 [Prometheus](https://docs.min.io/docs/how-to-monitor-minio-using-prometheus.html)

## 安装

由于 MinIO Server 已经提供了 Docker 的安装镜像，那我们就以 Docker 安装为例，其他安装方式可参考官方教程 [MinIO Quickstart Guide](https://docs.min.io/docs/minio-quickstart-guide.html)

关于 Docker 的安装这里不再赘述，Docker 相关详细的使用等知识，可参考我之前的文章 [Docker（一）]()

```docker
# 1. 拉取 minio docker 镜像
docker pull minio/minio
# 2. 运行 minio 服务
docker run -p 9000:9000 --name minio \
  -v /opt/docker/minio/data:/data \
  -v /opt/docker/minio/config:/root/.minio \
  -d --restart=always \
  -d minio/minio server /data
```

>这里简单说一下命令的含义，应用命名为 minio ，运行服务在 9000 端口，同时将容器的相关路径文件映射到宿主机的 `/opt/docker/minio` 路径，开机自启

成功运行服务，可查看日志

```text
Endpoint: http://172.17.0.2:9000 http://127.0.0.1:9000
Browser Access:
http://172.17.0.2:9000 http://127.0.0.1:9000
Object API (Amazon S3 compatible):
Go: https://docs.min.io/docs/golang-client-quickstart-guide
Java: https://docs.min.io/docs/java-client-quickstart-guide
Python: https://docs.min.io/docs/python-client-quickstart-guide
JavaScript: https://docs.min.io/docs/javascript-client-quickstart-guide
.NET: https://docs.min.io/docs/dotnet-client-quickstart-guide
Detected default credentials 'minioadmin:minioadmin', please change the credentials immediately using 'MINIO_ROOT_USER' and 'MINIO_ROOT_PASSWORD'
```

安装完成后，我们就可以通过 http://localhost:9000 访问 MinIO 服务，默认用户名和密码分别为: <mark>minioadmin</mark>, <mark>minioadmin</mark>

## 使用

### 页面操作

![](https://res.cloudinary.com/incoder/image/upload/v1617090692/blog/minio-web.png)

我们直接看图，输入账号密码后，可以看到 MinIO 的管理页面，我们就可以上传文件，是不是很方便。第一次上传必须先要创建一个 bucket 后，才可以上传，如下图操作结果

![](https://res.cloudinary.com/incoder/image/upload/v1617091291/blog/minio-upload.png)

### Client 操作

### SDK 操作

{% note warning %}
这里以 Java 语言为例，查看官方文档时，一定要查看英文文档，中文文档已年久失修落后很多，其他的语言实现请参考官方文档
{% endnote %}

#### 导入依赖

```groovy
dependencies {
  implementation "io.minio:minio:8.1.0"
}
```

#### 功能实现

由于我这里是 SpringBoot 项目，为了方便在应用的 `application.yml` 文件中配置了 MinIO 相关的参数

#### 配置文件

```yaml
minio:
  # minio 服务运行的地址
  endpoint: http://127.0.0.1
  # minio 服务运行的端口
  port: 9000
  # minio 服务登录账号
  accessKey: minioadmin
  # minio 服务登录密码
  secretKey: minioadmin
  # minio 设置上传默认存放桶
  bucketName: cpe-manager-test
```

#### 工具类

```java
/**
 * 资源上传工具类
 *
 * @author : Jerry xu
 * @since : 2021/3/18  14:05
 */
@Slf4j
@Component
public class MinioUtils {

    /**
     * minio:
     * endpoint: http://192.168.1.163
     * port: 9000
     * accessKey: minioadmin
     * secretKey: minioadmin
     * bucketName: cpe-manager-test
     */
    @Value("${minio.endpoint}")
    private static final String ENDPOINT = "http://192.168.1.163";
    @Value("${minio.port}")
    private static final Integer PORT = 19000;
    @Value("${minio.accessKey}")
    private static final String ACCESS_KEY = "minioadmin";
    @Value("${minio.secretKey}")
    private static final String SECRET_KEY = "minioadmin";
    @Value("${minio.bucketName}")
    private static final String BUCKET_NAME = "cpe-manager-test";

    private static MinioClient minioClient;

    public static MinioClient getInstance() {
        if (minioClient == null) {
            minioClient = MinioClient.builder().endpoint(ENDPOINT, PORT, false).credentials(ACCESS_KEY, SECRET_KEY).build();
        }
        return minioClient;
    }

    /**
     * 获取minio所有的桶
     *
     * @return java.util.List<io.minio.messages.Bucket>
     * @throws Exception exception
     */
    public static List<Bucket> getAllBucket() throws Exception {
        // 获取minio中所以的 bucket
        List<Bucket> buckets = getInstance().listBuckets();
        for (Bucket bucket : buckets) {
            log.info("bucket 名称:  {}      bucket 创建时间: {}", bucket.name(), bucket.creationDate());
        }
        return buckets;
    }

    /**
     * 将图片上传到minio服务器
     *
     * @param inputStream 输入流
     * @param objectName  存储的文件名称，必须包含后缀
     * @param bucketName  自定义存储桶
     */
    public static String uploadToMinio(InputStream inputStream, String objectName, String bucketName) {
        try {
            // 获取文件后缀
            String fileSuffix = Objects.requireNonNull(objectName).substring(objectName.lastIndexOf("."));
            String contentType = FileType.getContentType(fileSuffix);
//            // 重新生成文件名，避免重复
//            String objectName = UUID.randomUUID().toString() + fileSuffix;
            long size = inputStream.available();
            PutObjectArgs putObjectArgs = PutObjectArgs.builder()
                    .bucket(bucketName)
                    .object(objectName)
                    .stream(inputStream, size, -1)
                    .contentType(contentType)
                    .build();
            // 上传到minio
            ObjectWriteResponse objectWriteResponse = getInstance().putObject(putObjectArgs);
            inputStream.close();
            if (!StringUtils.isEmpty(objectWriteResponse.etag())) {
                // 返回上传获取到的地址
                return getUrlByObjectName(objectName);
            }
        } catch (Exception e) {
            log.error(e.getMessage());
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 将图片上传到minio服务器，默认存放在 cpe-manager-test 桶内
     *
     * @param inputStream 输入流
     * @param objectName  存储的文件名称，必须包含后缀
     */
    public static String uploadToMinio(InputStream inputStream, String objectName) {
        try {
            // 获取文件后缀
            String fileSuffix = Objects.requireNonNull(objectName).substring(objectName.lastIndexOf("."));
            String contentType = FileType.getContentType(fileSuffix);
//            // 重新生成文件名，避免重复
//            String objectName = UUID.randomUUID().toString() + fileSuffix;
            long size = inputStream.available();
            PutObjectArgs putObjectArgs = PutObjectArgs.builder()
                    .bucket(BUCKET_NAME)
                    .object(objectName)
                    .stream(inputStream, size, -1)
                    .contentType(contentType)
                    .build();
            // 上传到minio
            ObjectWriteResponse objectWriteResponse = getInstance().putObject(putObjectArgs);
            inputStream.close();
            if (!StringUtils.isEmpty(objectWriteResponse.etag())) {
                // 返回上传获取到的地址
                return getUrlByObjectName(objectName);
            }
        } catch (Exception e) {
            log.error(e.getMessage());
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 根据指定的objectName获取下载链接，需要bucket设置可下载的策略
     *
     * @param objectName 对象的名称
     * @return java.lang.String
     */
    public static String getUrlByObjectName(String objectName) {
        try {
            return getInstance().getPresignedObjectUrl(
                    GetPresignedObjectUrlArgs.builder()
                            .method(Method.GET)
                            .bucket(BUCKET_NAME)
                            .object(objectName)
                            // 过期策略【默认有效期7天】
//                            .expiry(2, TimeUnit.HOURS)
                            .build());
        } catch (Exception e) {
            log.error(e.getMessage());
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 根据objectName从minio中下载文件到指定的目录
     *
     * @param objectName minio上的文件名称
     * @param fileName   下载生成的文件名
     * @param dir        文件目录
     * @throws Exception exception
     */
    public static void downloadFromMinioToFile(String objectName, String fileName, String dir) throws Exception {
        GetObjectArgs objectArgs = GetObjectArgs.builder()
                .bucket(BUCKET_NAME)
                .object(objectName)
                .build();
        File file = new File(dir);
        if (!file.exists()) {
            if (file.mkdirs()) {
                log.error("创建失败");
            }
        }
        InputStream inputStream = getInstance().getObject(objectArgs);
        FileOutputStream outputStream = new FileOutputStream(new File(dir, fileName.substring(fileName.lastIndexOf("/") + 1)));
        int length;
        byte[] buffer = new byte[1024];
        while ((length = inputStream.read(buffer)) != -1) {
            outputStream.write(buffer, 0, length);
        }
        outputStream.close();
        inputStream.close();
    }

    /**
     * 根据文件名批量删除（默认删除 BUCKET_NAME 下的文件）
     *
     * @param listFile 文件名(含后缀)列表，例如：demo.png
     * @return 成功返回为null， 失败返回Map<objectName, failMessage>
     */
    @SneakyThrows
    public static Map<String, String> removeObjects(List<String> listFile) {
        List<DeleteObject> objects = new LinkedList<>();
        Map<String, String> resultMap = new HashMap<>();
        listFile.forEach(t -> objects.add(new DeleteObject(t)));
        Iterable<Result<DeleteError>> results =
                getInstance().removeObjects(
                        RemoveObjectsArgs.builder()
                                .bucket(BUCKET_NAME)
                                .objects(objects)
                                .build());
        for (Result<DeleteError> result : results) {
            DeleteError error = result.get();
            resultMap.put(error.objectName(), error.message());
            log.error("Error in deleting:{}, message{}", error.objectName(), error.message());
        }
        return resultMap;
    }

}
```

#### 上传接口

```java
@PostMapping(value = "/upload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
@ApiOperation(value = "文件上传", notes = "支持多文件上传")
public List<String> uploadTest(@ApiParam(value = "文件") @RequestParam("file") List<MultipartFile> file) {
    // 上传的图片地址
    List<String> successFile = new ArrayList<>(file.size());
    file.forEach(t -> {
        try {
            String url = MinioUtils.uploadToMinio(t.getInputStream(), t.getOriginalFilename());
            log.info("图片地址{}", url);
            successFile.add(url);
        } catch (IOException e) {
            e.printStackTrace();
        }
    });
    return successFile;
}

```

#### 测试



## 问题

### bucket命名

创建 bucket 时，命名不可以使用下划线符号 "<font color="red">_</font>"

### 账号密码修改

通过网页管理页面修改登录账号及密码，提示 "Credentials of this user cannot be updated through MinIO Browser." ，原因是安装应用时，并未显示的指定用户名和密码，可在运行启动时添加如下配置

```bash
-e "MINIO_ROOT_USER=admin" \
-e "MINIO_ROOT_PASSWORD=admin123456" \
```

### 最长7天有效

通过网页管理页面共享图片或者是使用 SDK 上传图片得到的图片 URL 地址，有效期最长为7天
```

mc config host add minio http://192.168.1.163:19000 minioadmin minioadmin --api S3v4
mc policy set public minio/cpe-manager-test

mc config host add minio http://127.0.0.1:9000 minioadmin minioadmin --api S3v4
mc policy set public minio/bucket (bucket修改成你自己的名字)
```

### 图片无法查看

1. 使用 SDK 上传时，需要注意设置content-type信息
2. 无权限查看


## 小结

关于 MinIO 还有很多知识点，本片只是站在使用者角度，把一些使用过程和问题进行了汇总，谈不上深度

## 参考

1. [Minio 手册](https://docs.min.io/cn/minio-quickstart-guide.html)
2. [Minio 示例](https://github.com/minio/minio-java/tree/release)
3. [Minio 修改密码](https://blog.csdn.net/tank99tank/article/details/109464325)
4. [Minio](https://www.jianshu.com/p/68ac0477291d)
5. [Minio 安装以及使用](https://www.cnblogs.com/gaohongyu/p/13986964.html)
6. [Minio 设置文件链接永久有效](https://blog.csdn.net/WuJiangang5112/article/details/112988074)