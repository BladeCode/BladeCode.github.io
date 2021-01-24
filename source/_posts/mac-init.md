---
title: MacBook Pro 初始化
date: 2018-11-10 09:44:46
categories: macOS
tag: [Build, Exp]
---

今天拿到了一辆跑车 MBP，虽然不是顶配，也能算上中等吧，废话不啰嗦，上来就是一顿操作猛如虎，最终效果就是唬

跑车的一些零配件来源地[Awesome Mac](https://github.com/jaywcjlove/awesome-mac/blob/master/README-zh.md)，[MacWK](https://www.macwk.com/) 一些破解软件集合地

软件的安装，这里不再赘述，这里主要对常用开发软件的配置进行记录

<!-- more -->

## JDK

作为Android开发者，JDK的安装那是少不了

### 下载

在Oracle 官网下载所需JDK 版本，这里举例：[JDK1.8.0_191](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

### 安装

此处省略，简单的安装步骤

### 配置

以下**命令**相关操作，均在自带系统**终端**应用或者自己安装的其他终端命令工具

```bash
# 查看安装的Java版本
java -version
# 编辑profile文件
sudo vim /etc/profile
# 在打开的 profile 文件中，最下面加入以下文本，添加完成后，保存退出
JAVA_HOME="/Library/Java/JavaVirtualMachines/jdk1.8.0_191.jdk/Contents/Home/"

CLASS_PATH="$JAVA_HOME/lib"

PATH=".:$PATH:$JAVA_HOME/bin"

# 使配置生效
source /etc/profile
# 查看JAVA_HOME是否正确
echo $JAVA_HOME
```

![jdk-command](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-jdk-comand.png)

![jdk-config](https://res.cloudinary.com/incoder/image/upload/v1541968115/blog/mac-jdk-config.png)

> 注意：
> 1. `JAVA_HOME` 中 `jdk1.8.0_191.jdk` 是自己安装对应版本的文件夹，可以在Finder中，快捷键：Command + Shift + G，输入： `/Library/Java/JavaVirtualMachines/` ，最终得到对应的文件夹名，如： `jdk1.8.0_191.jdk`
> 2. vim模式下，输入“i”：表示，插入，“esc”：表示退出编辑模式，“:wq!”：表示保存并退出

## MySQL

### 下载

官方下载地址：https://dev.mysql.com/downloads/

![mac-mysql-downlaod](https://res.cloudinary.com/incoder/image/upload/v1582517702/blog/mac-mysql-downlaod.png)

### 安装

![mac-mysql-install](https://res.cloudinary.com/incoder/image/upload/v1582523133/blog/mac-mysql-install.gif)

我这里选择自定义设置密码，请记住你设置的密码，最好是大小写+数字+字符的组合方式

### 登录

```mysql
# 使用 root 账号登录 MySQL
mysql -u root -p
# 出现下图中的MySQL，表示成功连接 MySQL
```

![mac-login-mysql](https://res.cloudinary.com/incoder/image/upload/v1582523478/blog/mac-login-mysql.png)

### 卸载

![mac-mysql-uninstall](https://res.cloudinary.com/incoder/image/upload/v1582517706/blog/mac-mysql-uninstall.png)

## Git

直接在自带系统**终端**应用中，输入 `git --version` ，由于之前并没有安装，系统会提示，直接同意并安装即可

### GitHub配置

```bash
# 查看本地是否生成过秘钥，如果该文件夹不存在，则表示未生成过秘钥
cd ~/.ssh
# 生成一个github的秘钥，这里github可以根据喜好自己命名（默认.ssh路径，不添加密码等操作，直接三次回车，即可生成秘钥）
ssh-keygen -t rsa -C "github"
# 查看公钥
cat ~/.ssh/id_rsa.pub
# 复制公钥添加到GitHub的SSH设置中，这里省略操作截图步骤
```
![mac-github-config](https://res.cloudinary.com/incoder/image/upload/v1542034940/blog/mac-github-config.png)

## Gradle配置

* 下载地址：[官网](http://services.gradle.org/distributions)，下载-all版本
* 设置GRADLE_HOME路径
    ``` bash
    # 打开.bash_profile文件
    open -e .bash_profile
    # Gradle_HOME环境设置，并保存
    GRADLE_HOME=/Users/blade/Documents/DevTools/Gradle/gradle-4.6
    export GRADLE_HOME
    export PATH=$PATH:$GRADLE_HOME/bin
    # 配置文件生效
    source ~/.bash_profile
    # 验证配置
    gradle -version
    ```

    >如果提示The file /Users/blade/.bash_profile does not exist.则在根路径下创建 `.bash_profile` 文件
    >执行命令 `touch .bash_profilesss`

    ![gradle-config](https://res.cloudinary.com/incoder/image/upload/v1541968116/blog/mac-gradle-config.png)

## Keka

对于 macOS 上的文件解压缩工具，有[The Unarchiver]()，[BetterZip](https://macitbetter.com/)，[Keka](https://www.keka.io/zh-cn/)等，我这里使用 keka，毕竟开源：https://github.com/aonez/Keka，真香

```bash
# 安装
brew cask install keka
# 卸载
brew cask zap keka
```

> [Mac 压缩 / 解压缩工具解决方案](https://sspai.com/post/46943)

## OpenInTerminal

OpenInTerminal 是在 Finder 上的一个扩展工具，能够快速在当前位置已命令行或者指定的编辑器打开，非常方便，对于 OpenInTerminal 和 OpenInTerminal-Lite，OpenInEditor-Lite 的区别

| Features | OpenInTerminal | OpenInTerminal-Lite & OpenInEditor-Lite |
| --- | --- | --- |
| Support Terminal, [iTerm](https://www.iterm2.com/), [Hyper](https://github.com/zeit/hyper), [Alacritty](https://github.com/jwilm/alacritty) and [kitty](https://sw.kovidgoyal.net/kitty/). | ✅ | ✅ |
| Support TextEdit, [Visual Studio Code](https://code.visualstudio.com/), [VSCode Insiders](https://code.visualstudio.com/insiders/), [Atom](https://atom.io/), [Sublime Text](https://www.sublimetext.com/), [VSCodium](https://github.com/VSCodium/vscodium), [BBEdit](https://www.barebones.com/products/bbedit/), [TextMate](https://macromates.com), [CotEditor](https://coteditor.com/), [MacVim](https://github.com/macvim-dev/macvim) and [JetBrains](https://www.jetbrains.com/)(AppCode, CLion, GoLand, IntelliJ IDEA, PhpStorm, PyCharm, RubyMine, WebStorm). | ✅ | ✅ |
| Set to open a new tab or window. | ✅ | ✅ |
| Support English, Chinese, French, Russian, Italian and Spanish. | ✅ | ✅ |
| Copy path of the selected file or Finder window to Clipboard | ✅ |  ❌ |
| GUI preferences | ✅ | ❌ |
| Support keyboard shortcuts. | ✅ | ❌ |
| Support Dark Mode. | ✅ | ❌ |

### 安装

* openinterminal
    ```bash
    brew cask install openinterminal
    ```
* openinterminal-lite & openineditor-lite
    ```bash
    brew cask install openinterminal-lite
    brew cask install openineditor-lite
    ```

### 配置

这里主要是对应用进行授权

* openinterminal
    System Preferences（系统偏好设置） -> Extensions（扩展） -> Finder Extensions（访达扩展）
* openinterminal-lite & openineditor-lite
    拖拽 openinterminal-lite & openineditor-lite 到你的 Finder 的状态栏上

## macOS 快捷键

顺带记录自己常用的快捷键吧🙃，其他用的时候在边用边查找吧


>[Mac 键盘快捷键](https://support.apple.com/zh-cn/HT201236)