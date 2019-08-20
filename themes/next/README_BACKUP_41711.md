<div align="right">官方文档:  
<a title="Chinese" href="docs/zh-CN/README.md">:cn:</a>
<a title="Russian" href="docs/ru/README.md">:ru:</a></div>

# <div align="center"><a title="Go to homepage" href="https://theme-next.org"><img align="center" width="56" height="56" src="https://raw.githubusercontent.com/theme-next/hexo-theme-next/master/source/images/logo.svg?sanitize=true"></a> e x T</div>

<p align="center">«NexT» is a high quality elegant <a href="http://hexo.io">Hexo</a> theme. It is crafted from scratch, with love.</p>

<p align="center">
  <a href="https://www.codacy.com/app/theme-next/hexo-theme-next?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=theme-next/hexo-theme-next&amp;utm_campaign=Badge_Grade"><img src="https://api.codacy.com/project/badge/Grade/72f7fe7609c2438a92069f448e5a341a" title="Project Grade"></a>
  <a href="https://travis-ci.org/theme-next/hexo-theme-next?branch=master"><img src="https://travis-ci.org/theme-next/hexo-theme-next.svg?branch=master" title="Travis CI [Linux]"></a>
  <a href="https://crwd.in/theme-next"><img src="https://d322cqt584bo4o.cloudfront.net/theme-next/localized.svg" title="Add or improve translation in few seconds!"></a>
  <a href="https://github.com/theme-next/hexo-theme-next/issues"><img src="http://isitmaintained.com/badge/open/theme-next/hexo-theme-next.svg" title="Percentage of issues still open"></a>
  <a href="https://github.com/theme-next/hexo-theme-next/issues"><img src="http://isitmaintained.com/badge/resolution/theme-next/hexo-theme-next.svg" title="Average time to resolve an issue"></a>
  <a href="https://github.com/theme-next/hexo-theme-next/releases"><img src="https://badge.fury.io/gh/theme-next%2Fhexo-theme-next.svg"></a>
  <a href="http://hexo.io"><img src="https://img.shields.io/badge/hexo-%3E%3D%203.5.0-blue.svg"></a>
  <a href="https://github.com/theme-next/hexo-theme-next/blob/master/LICENSE.md"><img src="https://img.shields.io/badge/license-%20AGPL-blue.svg"></a>
</p>

## 前言
这是一个fork项目，在[源项目](https://github.com/theme-next/hexo-theme-next)的基础上，集成了以下第三方常用的工具库
* [theme-next-algolia-instant-search](https://github.com/theme-next/theme-next-algolia-instant-search)：即时搜索
* [theme-next-bookmark](https://github.com/theme-next/theme-next-bookmark)：书签，用户可以保存他们的阅读位置
* [theme-next-fancybox3](https://github.com/theme-next/theme-next-fancybox3)：图片预览
* [theme-next-reading-progress](https://github.com/theme-next/theme-next-reading-progress)：页面阅读进度加载
* [theme-next-pace](https://github.com/theme-next/theme-next-pace)：顶部装载栏
* [theme-next-pangu](https://github.com/theme-next/theme-next-pangu)：字符间空格处理
* [theme-next-pdf](https://github.com/theme-next/theme-next-pdf)：PDF文件预览支持

## 使用

### 安装主题
在你的hexo项目中，安装已集成好的[NexT](https://github.com/RootCluster/hexo-theme-next)主题
```bash
# 在你的 blog 根目录下执行
git clone https://github.com/RootCluster/hexo-theme-next.git themes/next
```

### 个性化配置
在你的 blog 项目的根目录 `source/_data`中，创建`next.yml`

copy`<your blog>/themes/next/`路径下`_config.yml`的内容到`<your blog>/source/_data/`路径的`next.yml`文件中

以下配置均在`next.yml`文件中
* algolia-instant-search
  ```
  algolia_search:
    enable: true
  ```
* bookmark
  ```
  bookmark: true
  ```
* fancybox
  ```
  fancybox: true
  ```
* reading_progress
  ```
  reading_progress:
    enable: true
    color: "#37c6c0"
    height: 2px
  ```
* pace
  ```
  pace: true
  ```
* pangu
  ```
  pangu: true
  ```
* pdf
  ```
  # PDF Support
  pdf:
    enable: true

    # Default(true) will load PDFObject/PDF.js script on demand
    # That is it only render those page who has 'pdf: true' in Front Matter.
    # If you set it to false, it will load PDFObject/PDF.js srcipt EVERY PAGE.
    per_page: true

    height: 500px

    pdfobject:
      # Use 2.1.1 as default, cloudflare as default CDN
      cdn: //cdnjs.cloudflare.com/ajax/libs/pdfobject/2.1.1/pdfobject.min.js
  ```

## 更新
* 具体操作查看[Git 子仓库管理](https://incoder.org/2018/05/17/git-sub/#git-subtree-%E5%B8%B8%E7%94%A8%E6%93%8D%E4%BD%9C-%E9%87%8D%E7%82%B9)
* [Github上fork项目后保持与源项目更新](https://segmentfault.com/a/1190000008401427)