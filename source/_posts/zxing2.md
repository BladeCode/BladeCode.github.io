---
title: Zxing（二）Android 模块应用源码探索
date: 2019-05-02 22:41:10
categories: Android
tag: [Zxing]
---

[ZXing（“Zebra Crossing”](https://github.com/zxing/zxing)）用于Java，Android的条形码扫描库。虽然当前开源库仅处于维护模式，意味着更改是由贡献的补丁来驱动，只会考虑错误修复和次要的增强功能

本篇开启 [ZXing项目Android](https://github.com/zxing/zxing/tree/master/android) 模块的探索学习之路，那么首先我们要集成该模块到项目中

<!-- more -->

## 模块集成

* 下载官方项目[Zxing](https://github.com/zxing/zxing)
* 使用 AS 创建一个新的 Project

>编译环境
* Android studio：3.4
* gradle：5.1.1
* SDK：28
* JDK：1.8

演示项目[rc-android-zxing](https://github.com/RootCluster/rc-android-zxing)

### 导入步骤

* 导入 module
    ![import_module](https://raw.githubusercontent.com/RootCluster/rc-android-zxing/zxing/images/import_module.png)
* 选择 module
    ![select_import_module](https://github.com/RootCluster/rc-android-zxing/raw/zxing/images/select_import_module.png)
* 移除最小及目标版本设置
    ![remove_min_target_version](https://github.com/RootCluster/rc-android-zxing/raw/zxing/images/remove_min_target_version.png)
* 添加项目核心依赖
    ![import_dependencies](https://github.com/RootCluster/rc-android-zxing/raw/zxing/images/import_dependencies.png)
`com.google.zxing:android-core:3.3.0`：实质是[android-core](https://github.com/zxing/zxing/tree/master/android-core)模块  
`com.google.zxing:core:3.3.3`：实质是[core](https://github.com/zxing/zxing/tree/master/core)模块
* 删除`app`module（可选）

### 项目展示

![zxing](https://github.com/RootCluster/rc-android-zxing/raw/zxing/images/zxing.gif)

当然你也可以下载官方提供的应用[google play](https://play.google.com/store/apps/details?id=com.google.zxing.client.android)

### 异常问题处理

#### 相机出现问题

##### 表现

如果你运行的设备是 Android 6.0 以上版本，那么在启动应用程序后，应该会提示你“很遗憾，Android 相机出现问题，你可能需要重启设备”，如下图
![project_problem](https://github.com/RootCluster/rc-android-zxing/raw/zxing/images/project_problem.png)

##### 分析

分析运行日志，进行定位`CaptureActivity.java`类，在初始化相机时，由于没有相机权限，因此无法正常运行应用
![zxing_error_log](https://github.com/RootCluster/rc-android-zxing/raw/zxing/images/zxing_error_log.png)

##### 解决方式

```java
// CaptureActivity.jaca
// line 266 && line 443
mHolder = surfaceHolder;
if (Build.VERSION.SDK_INT > Build.VERSION_CODES.LOLLIPOP_MR1) {
    if (ContextCompat.checkSelfPermission(CaptureActivity.this,
           android.Manifest.permission.CAMERA) != PackageManager.PERMISSION_GRANTED) {
        // 先判断有没有权限 ，没有就在这里进行权限的申请
        ActivityCompat.requestPermissions(CaptureActivity.this,
            new String[]{Manifest.permission.CAMERA}, CAMERA_OK);
        } else {
             // 说明已经获取到摄像头权限了
             initCamera(surfaceHolder);
         }
    } else {
        initCamera(surfaceHolder);
    }

// line 803
@Override
public void onRequestPermissionsResult(int requestCode
        , @NonNull String[] permissions, @NonNull int[] grantResults) {
    // If request is cancelled, the result arrays are empty.
    if (requestCode == CAMERA_OK) {
        if (grantResults.length > 0
                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
            // permission was granted, yay! Do the
            // contacts-related task you need to do.
            initCamera(mHolder);
        }
    }
}
```
#### 其他问题

同样我们在`EncodeActivity.java`文件中加入读取内存卡的权限`READ_EXTERNAL_STORAGE`

## 项目分析

### 项目概要

```
rc-android-zxing
    ├── book/
    ├── camera/
    ├── clipboard/
    ├── encode/
    ├── history/
    ├── result/
    ├── share/   
    ├── wifi/
    ├── AmbientLightManager
    ├── BeepManager
    ├── CaptureActivity
    ├── CaptureActivityHandler
    ├── Contents
    ├── DecodeFormatManager
    ├── DecodeHandler
    ├── DecodeHintManager
    ├── DecodeThread
    ├── FinishListener
    ├── HelpHelper
    ├── InactivityTimer
    ├── Intents
    ├── IntentSource
    ├── LocaleManager
    ├── PreferencesActivity
    ├── PreferencesFragment
    ├── ScanFromWebPageManager
    ├── ViewfinderResultPointCallback
    └── ViewfinderView

```

## 项目源码

## 总结