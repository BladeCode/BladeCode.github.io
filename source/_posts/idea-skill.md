---
title: 开发小技巧 
date: 2019-01-02 15:00:10
categories: DevTool
tag: [Android Studio, JetBrains]
---

[Android Studio](https://zh.wikipedia.org/wiki/Android_Studio) 是Google基于JetBrains的 [IntelliJ IDEA](https://www.jetbrains.com/idea) 所定制开发的 Android 开发 IDE。因此这里的设置适用于 JetBrains 公司系列的开发工具，同样也适用于 Android Studio，这是一篇持续更新的文章，在平时的使用过程中一些习惯性的模板化的一些设置，可以减少我们一些重复性的操作，进而提高开发效率。

<!-- more -->

## 设置

快捷键：
* Windows：`Ctrl`+`Alt`+`S`
* macOS：`⌘`+`,`

### 样式

#### 约束提示/空格及缩进

* 描述：
    - 为了约束编写的代码过长而不换行，在代码编辑面板右侧右侧有个条竖线进行约束和警示，当然你可以关闭
    - 为了工整的显示代码的空格和换行是否正确，可以开启显示空格和缩进等样式
    
![idea-skill-line](https://res.cloudinary.com/incoder/image/upload/v1546416652/blog/idea-skill-line.png)

#### 窗口打开全部展示

* 描述：为了在编辑器中展示全部打开的文件(不限制在同一行)
    ![idea-open-windows-limit](https://res.cloudinary.com/incoder/image/upload/v1562844691/blog/idea-open-windows-limit.png)


### 颜色

#### 局部变量

* 描述：为了直观的区分出全局变量和局部变量，而不需要仔细阅读代码
![idea-skill-local](https://res.cloudinary.com/incoder/image/upload/v1546417187/blog/idea-skill-local.png)

#### 控制台日志

* 描述：为了直观在控制台上显示不同级别日志
![idea-logcat-color](https://res.cloudinary.com/incoder/image/upload/v1562845142/blog/idea-logcat-color.png)

颜色推荐：
* <font color=#AA66CC>Assert：AA66CC</font>
* <font color=#33B5E5>Debug：33B5E5</font>
* <font color=#FF6B68>Error：FF6B68</font>
* <font color=#99CC00>Info：99CC00</font>
* <font color=#BBBBBB>Verbose：BBBBBB</font>
* <font color=#FFBB33>Warning：FFBB33</font>

### 其他

#### Toolbar添加设置按钮

* 描述：在不方便使用快捷键打开设置时，原本的操作是：File-->Settings Repository...，因此调整到状态栏上
![idea-skill-settings](https://res.cloudinary.com/incoder/image/upload/v1546417198/blog/idea-skill-settings.png)

#### 不区分大小写

* 描述：在编码过程中，通常一些智能提示需要根据输入的支付来提示，而大小写不同对应的提示也不完全一致，因此取消智能提示对大小写字符的要求
![idea-skill-case](https://res.cloudinary.com/incoder/image/upload/v1546416873/blog/idea-skill-case.png)

#### 自动导包

* 描述：在编码过程中，一些无用或者需要引入的包，可设置成自动的方式，当无法自动导入或移除无用包时，再手动的去选择处理
![idea-skill-auto](https://res.cloudinary.com/incoder/image/upload/v1546416872/blog/idea-skill-auto.png)

#### 字段默认前缀
* 描述：为了让代码规范，我们会对变量前面设置默认前缀，那么 idea 也是支持
![idea-field-prefix](https://res.cloudinary.com/incoder/image/upload/v1562845918/blog/idea-field-prefix.png)

## 常用技巧

## 编码技巧

## 调试技巧

## 参考
* [IntelliJ-IDEA-Tutorial](https://github.com/judasn/IntelliJ-IDEA-Tutorial)