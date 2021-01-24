---
title: Hexo Blog 高级指南
date: 2020-11-20 18:18:18
categories: Hexo
tag: Build
---

[NexT](https://theme-next.js.org) 是 [Hexo](https://hexo.io/zh-cn/index.html) 非常受欢迎的博客主题，方便简洁，但却不简单的功能，你可以在提供的强大功能基础上进行扩展或者自定义，来满足你的个性化需求。本篇文章主要是对应 NexT 提供的一些高级功能的使用，作为一个持续更新的文章吧，记录自己 SX 操作，当然也是我平时在使用 NexT 时遇到的一些问题的记录。好了废话不多说了，我们直接进入正题

<!-- more -->

## 博客升级

每次对于 NexT 的升级或多或少都会遇到些问题，这次也不例外，首先是对于不同版本的管理，由于一些历史原因有三个组织仓库分别对应不同的版本域，升级是需要注意下，本次我是从 7.8.0 版本升级到 8.0.x 版本，以后跟随官方，每月更新 NexT

### npm 改成 yarn（可选）

>yarn 的安装，请自行根据你的系统去安装，我这里 macOS 使用命令即可 `brew install yarn`

1. 删除根目录的 `package-lock.json`，并在根目录执行 `hexo clean && rm -rf node_modules/`
2. 根目录下执行 `yarn install`

### 更改 NexT 主题仓库

1. 删除当前主题，在根目录下执行 `rm -rf themes/`
2. 安装新的主题，在根目录下执行 
   ```bash
   git clone https://github.com/next-theme/hexo-theme-next themes/next
   ```

### 修改配置

之前为了使主题更新不受影响，在项目的根目录 `source/_data` 路径下有一个 `next.yml` 文件来进行对 NexT 的自定义设置，那么在 8.0 版本开始，在项目根目录 `_config.{theme}.yml` 文件来代替之前在 `source/_data` 路径下的 `next.yml` 文件

### 问题

#### node --trace-warnings

##### 异常信息

由于 NexT 需要 Hexo5.0+，在升级到 NexT 8.0.x 版本警告信息如下

```log
(node:17336) Warning: Accessing non-existent property 'lineno' of module exports inside circular dependency
(Use `node --trace-warnings ...` to show where the warning was created)
(node:17336) Warning: Accessing non-existent property 'column' of module exports inside circular dependency
(node:17336) Warning: Accessing non-existent property 'filename' of module exports inside circular dependency
(node:17336) Warning: Accessing non-existent property 'lineno' of module exports inside circular dependency
(node:17336) Warning: Accessing non-existent property 'column' of module exports inside circular dependency
(node:17336) Warning: Accessing non-existent property 'filename' of module exports inside circular dependency
```

##### 原因分析

是由于 Hexo 项目嵌套依赖了 `stylus` 包，而对于 `0.54.5` 版本在 Node 14+ 版本存在问题，比如这里 `hexo-renderer-stylus` 包的依赖

```
……
│ │
├─┬ hexo-renderer-stylus@2.0.1
│ ├─┬ nib@1.1.2
│ │ └─┬ stylus@0.54.5
│ │   │ 
……
```

##### 解决方法

1. 可以降低你的 Node 版本到 12 版本
    ```bash
    brew uninstall node
    brew install node@12
    brew link --overwrite --force node@12
    ```
2. 推荐，更改替换 stylus 版本，在你的 `package.json` 文件中，添加如下配置
    ```json
    "resolutions": {
        "stylus": "^0.54.8"
    }
    ```

##### 总结

🌀 [pull-2538](https://github.com/stylus/stylus/pull/2538)
🐞 [issues-2534](https://github.com/stylus/stylus/issues/2534)
🛠 [solve-Accessing non-existent property](https://www.haoyizebo.com/posts/710984d0/)

#### hexo-douban

之前用了 hexo-douban 插件来进行对 books 和 movies 进行管理，在升级到 Node 14+版本上，当前的插件也停止工作了，异常日志如下

```log
INFO  0 books have been loaded in 1130 ms, because you are offline or your network is bad
INFO  0 movies have been loaded in 1329 ms, because you are offline or your network is bad
INFO  0 games have been loaded in 1004 ms, because you are offline or your network is bad
```

作者在🐞 [issues-2534](https://github.com/mythsman/hexo-douban/issues/77) 做了回复，暂时没有替代方案，故在新版中，我停止了 `hexo-douban` 插件的使用，挖个坑，等自己有时间或者有人修复此问题再或者有替代插件后再重新启用

1. 移除 hexo-douban 插件
    ```bash
    # yarn
    yarn remove hexo-douban
    # npm
    npm uninstall hexo-douban
    ```
2. 移除 `_config.yml` 配置文件中，douban 的相关的配置
3. 移除 `_config.{theme}.yml` 配置文件中，`menu` 配置的站点入口设置

## 博客评论

在 NexT version 8.1.0 版本，由于安全问题，[Valine被移除](https://github.com/next-theme/hexo-theme-next/issues/4#v8.1.0%20%E7%A7%BB%E9%99%A4%20Valine)，暂时我并未迁移 Valine 的评论

## 文章加密

对于 NexT 的文章，有时需要进行加密访问，那么该怎么去处理呢，其实这一点在 NexT 的生态里已经有了这样的插件，我们可以直接在使用在我们的 NexT 里面，只需要简单的配置

```bash
# npm 
npm i hexo-blog-encrypt --save
# yarn
yarn add hexo-blog-encrypt
```

加密优先级：文章信息头 > 按标签加密

### 站点配置（_config.yml）

#### 简单配置

```yml
# 文章密码访问 hexo-blog-encrypt
encrypt:
  enable: true
```

#### 更多配置

可以对一类（标签）来进行统一的密码设置

```yml
# 文章密码访问 hexo-blog-encrypt
encrypt: 
  abstract: 有东西被加密了, 请输入密码查看.
  message: 您好, 这里需要密码.
  tags:
  - {name: tagNameA, password: 密码A}
  - {name: tagNameB, password: 密码B}
  template: <div id="hexo-blog-encrypt" data-wpm="{{hbeWrongPassMessage}}" data-whm="{{hbeWrongHashMessage}}"><div class="hbe-input-container"><input type="password" id="hbePass" placeholder="{{hbeMessage}}" /><label>{{hbeMessage}}</label><div class="bottom-line"></div></div><script id="hbeData" type="hbeData" data-hmacdigest="{{hbeHmacDigest}}">{{hbeEncryptedData}}</script></div>
  wrong_pass_message: 抱歉, 这个密码看着不太对, 请再试试.
  wrong_hash_message: 抱歉, 这个文章不能被校验, 不过您还是能看看解密后的内容.
```

### 单文章配置

在你需要加密的文章前面，根据需要添加对应的参数，这里仅是一个示例

```markdown
---
title: Hello World
tags:
- 加密文章tag
date: 2020-11-20 18:18:18
password: helloworld
abstract: 该文章已加密, 请输入密码查看。
message: 该文章已加密, 请输入密码查看。
wrong_pass_message: 密码不正确，请重新输入！
wrong_hash_message: 文章不能被校验, 不过您还是能看看解密后的内容！
---
```
各参数说明
* password：文章密码
* abstract：文章摘要，会显示在博客的列表页
* message：文章查看时，密码输入框上面的描述性文字
* wrong_pass_message：校验失败提示
* wrong_hash_message：hash 验证失败

## 多语言

对于多语言，根据自身需要添加，默认，修改博客项目根目录 `_connfig.yml` 文件 `language` 属性即可
1. 对于单语言：language: xxx（具体语言可查看下方的官方说明）
2. 对于多语言：
   * 语言添加
        ```yml
        language:
           - zh-CN
           - en
        ```
   * 更改语言切换，`_config.{theme}.yml` 文件，`language_switcher`设置为 true
3. 字段定义，如果一些字段的翻译不是你想要的，你可以自行修改
   * 在根目录的 `source/_data` 文件夹下，创建 `languages.yml` 文件
   * 在文件中，修改对应语言的字段
        ```yml
        zh-CN:
          # items
          post:
            copyright:
              # the translation you perfer
              author: 本文博主
        en:
          menu:
            schedule: Calendar
        ```

>[多语言配置](https://theme-next.js.org/pisces/docs/theme-settings/internationalization.html?highlight=language)

## GitHub Action

## Hexo PWA

>由于暂未支持 Hexo5.0+版本，先占坑

## 参考

* [更新说明及常见问题](https://github.com/next-theme/hexo-theme-next/issues/4)
* [将 Hexo 升级到 v5.0.0](https://tommy.net.cn/2020/08/08/upgrade-hexo-to-v5-0-0/)
* [用 GitHub Actions 来自动部署 Hexo](https://tommy.net.cn/2020/08/06/deploy-hexo-with-github-actions/)
* [Hexo博客部署PWA](https://linwhitehat.github.io/Blog/2020/02/09/Hexo%E5%8D%9A%E5%AE%A2%E9%83%A8%E7%BD%B2PWA.html)
* [博客完美支持 PWA](https://sitoi.cn/posts/49115.html)
* [三步，让 Hexo 轻松支持 PWA](https://blog.decay.fun/2019/08/19/enhance-hexo-with-pwa-in-three-steps/)
* [Pwabuilder](https://www.pwabuilder.com/)
