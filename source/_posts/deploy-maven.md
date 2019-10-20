---
title: 发布 jar 到 Maven中央仓库
date: 2019-07-21 10:00:46
categories: Maven
tag: [Deploy]
---

## 背景

和朋友一起维护的开源组织（我就是打个辅助，逃~），其中有一个系列的项目，这些项目统一通过 base 项目的 pom 文件管理这个系列项目依赖的第三方 jar，其他一些辅助项目（如：tools）项目主要是一些常用工具方法的封装，为了能让我们在不同机器，不同地点能够无缝切换，更重要的让使用的伙伴能以最简便的方式运行（避免不必要的配置），我们需要把通用的东西托管起来，那么就需要将这些配置依赖或辅助 jar 托管到 Maven中央仓库，话不多说，就跟着我的步骤来看看如何将 jar 发布到 Maven中央仓库

## 准备

* Sonatype 账号
* GPG 
* 需要发布的项目

> 这里以 Mac 系统演示

## Sonatype

### 账号注册

Sonatype 账号注册地址：[https://issues.sonatype.org/secure/Signup!default.jspa](https://issues.sonatype.org/secure/Signup!default.jspa)

> 记录好你的账号和密码，后续会用到

### 创建 issue

创建 issue 地址：[https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134](https://issues.sonatype.org/secure/CreateIssue.jspa?issuetype=21&pid=10134)

填写信息时，只填写必填项即可

1. `Summary`：简单的项目介绍，填写一下
2. `Group Id`：项目ID，用来定位应用 jar 的坐标，可参考[官方说明](https://central.sonatype.org/pages/choosing-your-coordinates.html)
3. `Project URL`：项目主页地址
4. `SCM url`：Git仓库地址

>注意：
* Group Id
  * 无自己的域名：可以使用 Github，比如我的 GitHub 用户名是 BladeCode（也可以用你的组织名，如这里：twodragonlake），那么这里的Group Id应该填写 com.github.BladeCode，也可以使用 io.github.BladeCode
  * 有自己的域名：按照要求添加一条 TXT 的 DNS 解析，用来验证你的 `Group Id`
    ![ossrh-domain](https://res.cloudinary.com/incoder/image/upload/v1563762211/blog/ossrh-domain.png)
* 可参考：[OSSRH-45597](https://issues.sonatype.org/browse/OSSRH-45597)

### 验证 Group Id

根据你是否有自己的域名，有不同的方式来验证，上面的创建 issue 的注意中已经说明了，这里不啰嗦了，直接看下图
![ossrh-ticket](https://res.cloudinary.com/incoder/image/upload/v1563705782/blog/ossrh-ticket.png)

## GPG

* Windows：[Gpg4win](https://www.gpg4win.org/download.html)
* macOS：gpg

### 安装

macOS 为例
```sh
# 安装
brew install gpg
# 验证
gpg --version
```

### 生成秘钥对

```sh
gpg --gen-key
```
![ossrh-gpg-key](https://res.cloudinary.com/incoder/image/upload/v1563706827/blog/ossrh-gpg-key.png)

这里用于生成秘钥的用户名和邮箱，可以和你的 `Sonatype` 账号不一样，记录`密码`，在部署时需要用到
### 上传秘钥

```sh
# 上传秘钥，最好带上端口号
gpg --keyserver hkp://pool.sks-keyservers.net:11371 --send-keys <密钥ID>
# 验证秘钥，最好带上端口号
gpg --keyserver hkp://pool.sks-keyservers.net:11371 --recv-keys <密钥ID>
```
![ossrh-gpg-send](https://res.cloudinary.com/incoder/image/upload/v1563707069/blog/ossrh-gpg-send.png)

上传到其他服务器，命令同上，更换地址即可
* hkp://keyserver.ubuntu.com:11371
* hkp://keys.gnupg.net:11371

如果你忘记了你刚刚生成的秘钥，可以使用下面的命令来查看本地生成的所有秘钥
```sh
gpg --list-keys
```
## 配置

### maven 配置

* 查看路径
    macOS 可以使用 IDEA 查看 maven 的路径，`/usr/local/Cellar/maven/3.6.0/libexec/conf`
    ![ossrh-maven-local](https://res.cloudinary.com/incoder/image/upload/v1563707961/blog/ossrh-maven-local.png)
* 修改 `settings.xml` 文件
    在 `<servers>` 标签内，添加如下配置
    ```xml
    <server>
        <id>oss</id>
        <username>你注册的Sonatype账号</username>
        <password>密码</password>
    </server>
    ```

### pom 配置

配置你需要上传项目的 `pom` 文件

```xml
    <parent>
        <groupId>org.sonatype.oss</groupId>
        <artifactId>oss-parent</artifactId>
        <version>7</version>
    </parent>

    <name>tdl-base</name>
    <description>TwoDragonLake base pom</description>
    <url>https://github.com/TwoDragonLake/tdl-base</url>

    <licenses>
        <license>
            <name>Apache License, Version 2.0</name>
            <url>http://www.apache.org/licenses/LICENSE-2.0</url>
        </license>
    </licenses>

    <developers>
        <developer>
        <!-- 这里配置 gpg 生成秘钥时的用户名和邮箱 -->
            <name>Jerry xu</name>
            <email>incoder.xu@gmail.com</email>
        </developer>
    </developers>
    <!-- 更改成你的项目信息 -->
    <scm>
        <tag>master</tag>
        <url>https://github.com/TwoDragonLake/tdl-base.git</url>
        <connection>scm:git:https://github.com/TwoDragonLake/tdl-base.git</connection>
        <developerConnection>scm:git:https://github.com/TwoDragonLake/tdl-base.git</developerConnection>
    </scm>

    <profiles>
        <profile>
            <!-- 这个id有用的，用于发布命令 mvn clean deploy -P release（这个参数） -Dmaven.test.skip=true -->
            <id>release</id>
            <build>
                <pluginManagement>
                    <plugins>
                        <plugin>
                            <artifactId>maven-clean-plugin</artifactId>
                            <version>3.0.0</version>
                        </plugin>
                        <plugin>
                            <groupId>org.apache.maven.plugins</groupId>
                            <artifactId>maven-source-plugin</artifactId>
                            <version>3.0.1</version>
                            <executions>
                                <execution>
                                    <phase>package</phase>
                                    <goals>
                                        <goal>jar-no-fork</goal>
                                    </goals>
                                </execution>
                            </executions>
                        </plugin>
                        <plugin>
                            <artifactId>maven-compiler-plugin</artifactId>
                            <version>3.7.0</version>
                        </plugin>
                        <plugin>
                            <artifactId>maven-surefire-plugin</artifactId>
                            <version>2.20.1</version>
                        </plugin>
                        <plugin>
                            <artifactId>maven-jar-plugin</artifactId>
                            <version>3.0.2</version>
                        </plugin>
                        <plugin>
                            <artifactId>maven-install-plugin</artifactId>
                            <version>2.5.2</version>
                        </plugin>
                        <plugin>
                            <artifactId>maven-deploy-plugin</artifactId>
                            <version>2.8.2</version>
                        </plugin>
                    </plugins>
                </pluginManagement>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-compiler-plugin</artifactId>
                        <configuration>
                            <source>8</source>
                            <target>8</target>
                        </configuration>
                    </plugin>
                    <!-- Source -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-source-plugin</artifactId>
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar-no-fork</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                    <!-- Javadoc -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <version>2.9.1</version>
                        <!-- Skip javadoc error -->
                        <!-- <configuration>
                            <failOnError>false</failOnError>
                            <doclint>none</doclint>
                        </configuration> -->
                        <executions>
                            <execution>
                                <phase>package</phase>
                                <goals>
                                    <goal>jar</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                    <!-- Gpg Signature -->
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-gpg-plugin</artifactId>
                        <version>1.6</version>
                        <executions>
                            <execution>
                                <phase>verify</phase>
                                <goals>
                                    <goal>sign</goal>
                                </goals>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
            <!-- 这个也是必须要的 以下两个<id>代码块中的id要与 setting.xml中的id一致 -->
            <distributionManagement>
                <snapshotRepository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
                </snapshotRepository>
                <repository>
                    <id>oss</id>
                    <url>https://oss.sonatype.org/service/local/staging/deploy/maven2/</url>
                </repository>
            </distributionManagement>
        </profile>
    </profiles>
```

### 编译并部署

```sh
mvn clean deploy -P release -Dmaven.test.skip=true
```

然后在命令行的弹出中输入使用 gpg 命令生成秘钥时输入的密码，如果在命令行中没有弹框提示，那么可以在终端中输入`export GPG_TTY=$(tty)`命令，再次执行部署命令，部署完成参考下图提示
![ossrh-deploy](https://res.cloudinary.com/incoder/image/upload/v1563725146/blog/ossrh-deploy.png)

## 发布

### 编译构建验签

部署成功后，使用`Sonatype`登录 [https://oss.sonatype.org](https://oss.sonatype.org/#stagingRepositories)网站，进行发布，在`Build Promotion`中选择`Staging Repositories`，然后选择对应你的`groud id`的`Repository`，进行 close，这里的`Close`其实就是进行自动构建，进行验证
![ossrh-close](https://res.cloudinary.com/incoder/image/upload/v1563725940/blog/ossrh-close.png)

参看是自动化运行过程否有错误，正确如下截图没有错误提示，如果有错误提示，就按照提示内容进行处理
![ossrh-build](https://res.cloudinary.com/incoder/image/upload/v1563726108/blog/ossrh-build.png)

### 发布

构建成功无错误，后就可以发布了，其实发布和部署是一样的操作，只不过部署是进行`Close`，而发布是`Release`操作，此时会提示你发布成功后会删除`Staging Repositories` 的 `Repository` 记录
![ossrh-release](https://res.cloudinary.com/incoder/image/upload/v1563726307/blog/ossrh-release.png)

### 查看

打开你的[https://issues.sonatype.org](https://issues.sonatype.org/browse/OSSRH-45597)，登录并查看，你的 issues 下，提示你，已经发布成功，稍后可以在[https://search.maven.org](https://search.maven.org)中搜索到
![ossrh-deploy-success](https://res.cloudinary.com/incoder/image/upload/v1563726729/blog/ossrh-deploy-success.png)

搜索结果，可以查看到我们发布的包
![ossrh-search](https://res.cloudinary.com/incoder/image/upload/v1563726893/blog/ossrh-search.png)

## 异常

### IDEA 中 not found

项目中引入的 jar，IDEA 中提示无法找到包，可以打开 IDEA 的 Preferences 中进行同步
![ossrh-idea-update](https://res.cloudinary.com/incoder/image/upload/v1563727259/blog/ossrh-idea-update.png)

### 本地编译错误

* 无提示框提示输入密码
    ![ossrh-local-build-comfrim](https://res.cloudinary.com/incoder/image/upload/v1563727360/blog/ossrh-local-build-comfrim.png)
    解决方法：`export GPG_TTY=$(tty)` 命令，重新编译
* 无法连接sonatype
    ![ossrh-local-sonatype](https://res.cloudinary.com/incoder/image/upload/v1563727678/blog/ossrh-local-sonatype.png)
    解决方法：查看`settings.xml`文件中 `<server>`标签中配置的 `id` 是否与项目`pom`文件的`<distributionManagement>` 标签下的 `id` 是否一致

### sonatype 构建错误

查看构建过程中错误提示，我这里是应为无法验证签名，因此我将提示中的服务器地址，全部都再发布`gpg`的秘钥，注意地址开头是 `hkp`
![ossrh-build-error](https://res.cloudinary.com/incoder/image/upload/v1563728038/blog/ossrh-build-error.png)

## 附录

### 官方

* [OSSRH Guide](http://central.sonatype.org/pages/ossrh-guide.html)
* [发布要求、规范](https://central.sonatype.org/pages/apache-maven.html)
* [PGP签名使用](http://central.sonatype.org/pages/working-with-pgp-signatures.html)
* [发布项目文档](http://central.sonatype.org/pages/producers.html)

### 其他

* [发布 Maven 构件到中央仓库](http://www.r9it.com/20190701/maven-artifact-deploy.html)
* [发布构件到Maven中央仓库](https://silloy.me/2018/06/19/%E5%8F%91%E5%B8%83%E6%9E%84%E4%BB%B6%E5%88%B0Maven%E4%B8%AD%E5%A4%AE%E4%BB%93%E5%BA%93/)
* [如何发布Jar包到Maven Central Repository](https://www.jianshu.com/p/1bd36edab4ee)
