---
title: Flutter（一）之环境搭建
date: 2018-12-16 02:14:59
categories: Android
tag: [flutter]
---

这两年随着前端的高速发展，大前端的趋势下，Native移动应用开发市场在一定程度上被前端瓜分，加之硬件的快速迭代，性能已不存在明显的短板，[React Native](https://facebook.github.io/react-native)，[Vue](https://cn.vuejs.org/index.html)，[Angular](https://angular.io/)等等这些Web框架，对移动端也有了较大的提升，毕竟这样的开发效率会直线上升，并且大大减少了成本。技术的革新真的好快，如果不去学习，很快就会被淘汰

那就直接进入正题，[flutter](https://flutter.io/)是一站式跨平台解决方案，一次开发，适配整个移动平台，并且是由Google进行主导开发，开源的一个项目，现如今已经迭代到1.0版本

本篇文章主要记录在macOS系统上搭建flutter开发环境的过程

## 准备
* Android Studio开发环境（JDK，AndroidSDK，Gradle等等，这里不再赘述）
* [flutter SDK](https://flutter.io/docs/get-started/install)
* Android Studio Plugin --> Flutter

## 步骤
1. 解压下载的flutter SDK，并配置环境变量，例如这里配置在`.bash_profile`文件中
    ```bash
    # 打开 .bash_profile文件
    vim .bash_profile
    # .bash_profile文件中加入flutter sdk路径并保存
    export FLUTTER_HOME=/Users/blade/Documents/DevTools/flutter
    export PATH=$FLUTTER_HOME/bin:$PATH
    # 重新加载.bash_profile文件
    source .bash_profile
    ```
2. 检查环境变量是否配置正确，如果有相关命令说明，表示已配置好环境变量
    ```bash
    flutter -h
    ```
3. 检查开发环境，第一次执行，应该提示如下图所示说明
    ```bash
    flutter doctor
    ```
    ![flutter-doctor](https://res.cloudinary.com/incoder/image/upload/v1544994568/blog/flutter-doctor.png)
    其实不难，看出我们需要安装一下其他辅助工具等
4. 解决问题，按照如下命令，一步步执行，大概得1个小时左右（取决于你的网络情况）
    ```bash
    # 允许协议（android-licenses
    flutter doctor --android-licenses
    # 安装libimobiledevice
    brew install --HEAD libimobiledevice
    # 安装ideviceinstaller
    brew install ideviceinstaller
    # 安装ios-deploy
    brew install ios-deploy
    # 安装cocoapods
    brew install cocoapods
    # cocoapods 初始化，这一步比较耗时，需要下载文件大致547M，需要耐心等待
    pod setup
    ```
5. 以上步骤都正常运行后，再次检查环境，如下图所示结果，表示已完成flutter环境搭建
    ```bash
    flutter doctor
    ```

    ![flutter-finish](https://res.cloudinary.com/incoder/image/upload/v1544994676/blog/flutter-finish.png)

## 辅助
如果你不习惯或者不想使用Android Studio来开发Flutter，那么使用[VS Code](https://code.visualstudio.com)是最佳推荐的文本编辑器，只需要在VS Code中安装[Flutter](https://marketplace.visualstudio.com/items?itemName=dart-code.flutter)插件即可，它已包含所需的[Dart](https://marketplace.visualstudio.com/items?itemName=dart-code.dart-code)语法插件

关于程序的运行，那么模拟器当然少不了，这里介绍下macOS上如何启动Android 模拟器
* 首先AndroidSDK的环境变量配置少不了
* 配置emulator
    ```bash
    export ANDROID_HOME=/Users/blade/Library/Android/sdk
    export FLUTTER_HOME=/Users/blade/Documents/DevTools/flutter
    export PATH=$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools:$FLUTTER_HOME/bin:$PATH
    ```
* 启动
    ```bash
    # 查看已创建模拟器清单
    emulator -list-avds
    # 选择需要启动的模拟器，avd_name：表示从上面列表获取到的模拟器名称
    emulator -avd [avd_name]
    ```
>注意：
>* 不推荐使用[Genymotion](https://www.genymotion.com/)，flutter的运行在此模拟器上有各种灵异bug
>* PANIC: Missing emulator engine program for 'x86' CPU.解决方式：创建一个x64的模拟器

## 问题

### libusbmuxd version error during flutter install
```bahs
brew update
brew uninstall --ignore-dependencies libimobiledevice
brew uninstall --ignore-dependencies usbmuxd
brew install --HEAD usbmuxd
brew unlink usbmuxd
brew link usbmuxd
brew install --HEAD libimobiledevice
```

### Unbrewed header files were found in /usr/local/include

![flutter-node](https://res.cloudinary.com/incoder/image/upload/v1544994898/blog/flutter-node.png)

## 附录
* [flutter docs](https://flutter.io/docs)
* [Flutter免费视频第一季-环境搭建](http://jspang.com/post/flutter1.html#toc-586)
* [flutter安装记录过程](https://www.jianshu.com/p/637796e9c0ea)
* [macOS上搭建Flutter开发环境](https://flutterchina.club/setup-macos/#%E8%AE%BE%E7%BD%AE%E6%82%A8%E7%9A%84android%E8%AE%BE%E5%A4%87)
* [官方命令行构建您的应用](https://developer.android.google.cn/studio/build/building-cmdline?hl=zh-cn)