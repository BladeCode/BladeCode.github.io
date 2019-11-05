<div align="right">????:  
<a title="Chinese" href="docs/zh-CN/README.md">:cn:</a>
<a title="Russian" href="docs/ru/README.md">:ru:</a></div>

# <div align="center"><a title="Go to homepage" href="https://theme-next.org"><img align="center" width="56" height="56" src="https://raw.githubusercontent.com/theme-next/hexo-theme-next/master/source/images/logo.svg?sanitize=true"></a> e x T</div>

<p align="center">?NexT? is a high quality elegant <a href="http://hexo.io">Hexo</a> theme. It is crafted from scratch, with love.</p>

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

## ??
????fork????[???](https://github.com/theme-next/hexo-theme-next)???????????????????
* ~~[theme-next-algolia-instant-search](https://github.com/theme-next/theme-next-algolia-instant-search)?????~~
* ~~[theme-next-bookmark](https://github.com/theme-next/theme-next-bookmark)?????????????????~~
* ~~[theme-next-fancybox3](https://github.com/theme-next/theme-next-fancybox3)?????~~
* ~~[theme-next-reading-progress](https://github.com/theme-next/theme-next-reading-progress)?????????~~
* ~~[theme-next-pace](https://github.com/theme-next/theme-next-pace)??????~~
* ~~[theme-next-pangu](https://github.com/theme-next/theme-next-pangu)????????~~
* [theme-next-pdf](https://github.com/theme-next/theme-next-pdf)?PDF??????

## ??

### ????
???hexo???????????[NexT](https://github.com/RootCluster/hexo-theme-next)??
```bash
# ??? blog ??????
git clone https://github.com/RootCluster/hexo-theme-next.git themes/next
```

### ?????
??? blog ?????? `source/_data`????`next.yml`

copy`<your blog>/themes/next/`???`_config.yml`????`<your blog>/source/_data/`???`next.yml`???

??????`next.yml`???
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

## ??
* ??????[Git ?????](https://incoder.org/2018/05/17/git-sub/#git-subtree-%E5%B8%B8%E7%94%A8%E6%93%8D%E4%BD%9C-%E9%87%8D%E7%82%B9)
* [Github?fork???????????](https://segmentfault.com/a/1190000008401427)