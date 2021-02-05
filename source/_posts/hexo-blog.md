---
title: Hexo Blog 搭建
date: 2018-03-25 01:18:26
categories: Hexo
tag: Build
---

之前一直纠结用[Jekyll](https://jekyllrb.com)还是[Hexo](https://hexo.io)来搭建[GitHub Page](https://pages.github.com)博客，原本一直想搭建一个[Material Design](https://material.io/guidelines)主题风格，从[Hexo Themes](https://hexo.io/themes)中寻找到一款不错的主题，[indigo](https://github.com/yscoder/hexo-theme-indigo)是一款支持IE10+，评论，目录导航，分享等功能的轻量Blog主题。

简单的修改了该主题之后，本地预览都没有什么问题，但是部署到[Github]()上，样式什么的都无法加载，应该是我的操作姿势不对吧，调整了半天没有解决，烦躁中找到之前star的另一款很受欢迎的[Next](https://github.com/iissnan/hexo-theme-next)主题。

既然自己修改的无法正常部署预览，那就用别人写好的吧，刚好赶上[Next](https://github.com/theme-next/hexo-theme-next)新版本V6.0系列的推出，那就不废话，直接开干

<!-- more -->

## 材料准备

* [Node LTS](https://nodejs.org/en/download)
* [Git](https://git-scm.com/downloads)
* [Hexo](https://hexo.io)
* [Next](https://github.com/theme-next/hexo-theme-next)

## 安装

`Node`,`Git`的安装过程略

### Hexo

1. Hexo 安装
    ``` bash
    $ npm install hexo-cli -g
    ```
2. 初始化
    ``` bash
    $ hexo init <your blog name>
    ```
3. 安装依赖包
    ``` bash
    $ cd <you blog name>
    $ npm install
    ```
4. 启动服务预览
    ``` bash
    $ hexo serve
    ```

### Next

1. 安装Next 主题
    ``` bash
    $ git clone https://github.com/theme-next/hexo-theme-next themes/next
    ```
    > 当前操作在 `blog`的根目录下执行

2. 修改Blog 配置
`you blog name` 根目录 `_config.yml`
    * theme: 由原来默认`landscape`更改位`next`(大约：76行)
    * 其他配置项，根据自己的需求进行更改，我这里更改了`title`,`subtitle`,`author`,`language`,`url`配置，其中`language`如果没有修改，默认为英文语言，在V6.0系列由原来`zh-Hans`更新为`zh-CN`
    * 添加部署到Github配置
    ``` bash
    deploy:
    type: git
    repo: https://github.com/BladeCode/BladeCode.github.io.git # 用户名仓库
    branch: master # 用户名仓库的分支应该指定master，master分支也可以不用写
    ```

3. 修改Theme 配置
路径：`you blog name`/Themes/next/_config.yml
这里不罗嗦了，其配置可参考[hexo-theme-next](https://github.com/iissnan/hexo-theme-next)项目`README`文件

### 部署

上面已经配置好了部署的目标仓库，那么这里直接使用Hexo提供的部署命令即可
``` bash
$ hexo d
```
相关命令介绍等，请查看[官方文档说明](https://hexo.io/docs)

部署完成后，可以直接访问 http://`you blog name`/github.io

## 自定义域名

虽然现在 blog 可以使用 Github 提供的项目二级域名来访问，为了个性化以及方便等，配置自己的域名
1. 登录域名所属的管理网站(这里以阿里云域名服务为例)
    ![gitpages-domain-manger](https://res.cloudinary.com/incoder/image/upload/v1525516603/blog/gitpages-domain-manger.png)
2. 添加解析
    ``` bash
    $ # 解析一
    记录类型：CNAME
    主机记录：www
    记录值：bladecode.github.io
    解析路线：default

    $ # 解析二
    记录类型：A
    主机记录：@
    记录值：192.30.252.153
    解析路线：default

    $ # 解析三
    记录类型：A
    主机记录：@
    记录值：192.30.252.154
    解析路线：default
    ```
    > 192.30.252.153是GitHub的地址，你也可以ping你的 http://xxxx.github.io 的ip地址，填入进去

3. 修改Github上项目的domain设置
    ![gitpages-domain-custom](https://res.cloudinary.com/incoder/image/upload/v1525516630/blog/gitpages-domain-custom.png)
4. 添加CNAME文件
保存路径：`you blog name`/source
新增文件：CNAME 文件 (格式要求:`保存成所有文件而不是txt文件`)
CNAME 文件内容：`incoder.org`
> 如果带有www，那么以后访问的时候必须带有www完整的域名才可以访问，但如果不带有www，以后访问的时候带不带www都可以访问。所以建议，不要带有www

## Https开启

开启Https 需要借助[Cloudflare](https://www.cloudflare.com)，关于Cloudflare的介绍等不在这里展开
1. 注册账号
2. Add website
    ![site](https://res.cloudinary.com/incoder/image/upload/v1525516650/blog/gitpages-https-add-site.png)
3. Querying your DNS
    ![query](https://res.cloudinary.com/incoder/image/upload/v1525516664/blog/gitpages-https-dns-query.png)
4. Select Plan
    ![plan](https://res.cloudinary.com/incoder/image/upload/v1525516681/blog/gitpages-https-select-plan.png)
5. 域名解析记录获取
    ![continue](https://res.cloudinary.com/incoder/image/upload/v1525516694/blog/gitpages-https-continue.png)
6. DNS 对比，并修改[Cloudflare]()提供的DNS来解析
    ![change](https://res.cloudinary.com/incoder/image/upload/v1525516714/blog/gitpages-https-change-dns.png)
7. 域名管理后台，修改DNS
    ![dns](https://res.cloudinary.com/incoder/image/upload/v1525516733/blog/gitpages-https-wanwang-dns.png)
    > 阿里云服务相关域名DNS修改帮助[文档](https://help.aliyun.com/knowledge_detail/39844.html)
8. 成功激活
    ![active](https://res.cloudinary.com/incoder/image/upload/v1525516756/blog/gitpages-https-active.png)
9. SSL证书申请提醒
    ![cer](https://res.cloudinary.com/incoder/image/upload/v1525516994/blog/gitpages-https-ssl-cer.png)
10. 添加强制HTTPS规则
    ![rule](https://res.cloudinary.com/incoder/image/upload/v1525517025/blog/gitpages-https-page-rule.png)
11. 规则制定
    ![deploy](https://res.cloudinary.com/incoder/image/upload/v1525517045/blog/gitpages-https-deploy-https.png)

好了剩下的就是等证书颁发，可能要等上一些时间，具体每个人不尽相同，这里就不多做解释了。

Let's all，本次的Hexo的相关初级教程就到这里
