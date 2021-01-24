---
title: Gradle（二）Android
date: 2020-12-15 09:00:46
categories: Gradle
tag: [Gradle]
---

在上一篇 Gradle 的文章中，已经对 Gradle 有了一定的认识，Gradle 在 Android 有着广泛的应用，用作 Android 包依赖管理，应用构建，测试，等一些列自动化，我们本篇就来了解下在 Android 领域 Gradle 的使用。其实 Android 项目结构和之前在第一篇 Gradle 项目结构基本相同，只是在 module 级别多了的 proguard-rules.pro。对于不管是 Android项目或是 Spring 系列项目的子 module 都会有 build.gradle 文件

<!-- more -->

## Project 级别

### build.gradle

```groovy
// gradle 脚本执行所需依赖，分别是对应的maven库和插件
buildscript {
    repositories {
        google()
        jcenter()
    }
    // 声明依赖 Android Gradle 插件版本
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.3'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

// 项目所有 module 配置需要的依赖
allprojects {
    repositories {
        google()
        jcenter()
    }
}

// 一个 clean 任务，用于删除 build 目录的文件
task clean(type: Delete) {
    delete rootProject.buildDir
}
```

### settings.gradle

```groovy
// 默认指的是创建 Android 项目生成的 app 模块，也是默认的应用启动模块
include ':app'
```

## Module 级别

```groovy
// 表示这是一个应用程序模块，可直接运行
apply plugin: 'com.android.application'

// 编译时间
static def releaseTime() {
    return new Date().format('yyyy-MM-dd', TimeZone.getTimeZone('UTC'))
}

android {
    // 编译 Android 版本
    compileSdkVersion 29
    // 默认配置
    defaultConfig {
        // 应用 ID，手机中用于识别应用的唯一标识
        applicationId "org.incoder.android"
        // 目标 Android 版本
        targetSdkVersion 29
        // 申明应用可超过 65536 的方法，可参考：https://developer.android.google.cn/studio/build/multidex?hl=zh_cn
        multiDexEnabled true
        // 申明要使用AndroidJUnitRunner进行单元测试
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }

    // 签名配置，相关信息放置在 gradle.properties 文件中
    signingConfigs {
        debug {
            storeFile file(DEBUG_STORE_FILE)
            storePassword DEBUG_STORE_PASSWORD
            keyAlias DEBUG_KEY_ALIAS
            keyPassword DEBUG_KEY_PASSWORD
        }
        release {
            storeFile file(RELEASE_STORE_FILE)
            storePassword RELEASE_STORE_PASSWORD
            keyAlias RELEASE_KEY_ALIAS
            keyPassword RELEASE_KEY_PASSWORD
            v2SigningEnabled true
        }
    }

    buildTypes {
        debug {
            minifyEnabled false
            zipAlignEnabled false
            shrinkResources false
            // 签名
//            signingConfig signingConfigs.debug
            manifestPlaceholders = [
                    //JPush
                    JPUSH_APPKEY : "",
                    JPUSH_CHANNEL: "",
                    // Pgy
                    PGYER_APPID  : "7907554687e4c116316feedb3820ce52",
                    // Bugly
                    BUGLY_APPID  : "",
                    VERSION_NAME : "0.1.0",
            ]
            ndk {
                // 设置支持的SO库架构
                abiFilters 'armeabi', 'x86', 'armeabi-v7a', 'x86_64', 'arm64-v8a'
            }
        }

        release {
            // 混淆
            minifyEnabled false
            // Zipalign优化
            zipAlignEnabled true
            // 移除无用的resource文件
            shrinkResources false
            // 前一部分代表系统默认的android程序的混淆文件，该文件已经包含了基本的混淆声明
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            // 签名
            signingConfig signingConfigs.release
            // AppAnalytics key
            manifestPlaceholders = [
                    // JPush
                    JPUSH_APPKEY : "",
                    JPUSH_CHANNEL: "",
                    // Pgy
                    PGYER_APPID  : "7907554687e4c116316feedb3820ce52",
                    // Bugly
                    BUGLY_APPID  : "",
                    VERSION_NAME : "0.1.0",
            ]
        }
    }

    // 重命名安装包
    android.applicationVariants.all {
        variant ->
            variant.outputs.all {
                output ->
                    output.outputFileName = variant.flavorName + buildType.name +
                            "_" + releaseTime() + ".apk"
            }
    }

    // 产品变种
    flavorDimensions "minSDK"

    // 针对不同渠道的配置
    productFlavors {
        // 测试环境渠道包
        dev {
            applicationId 'org.incoder.test'
            minSdkVersion 19
            // 测试环境IP配置 API 接口地址
            buildConfigField 'String', 'API', '"http://xxx.xxx.xxx.xxx:8888"'
            versionCode 2020122501
            versionName "2.0"
            // 指定产品变种
            dimension "minSDK"
        }
        // 正式环境渠道包
        rel {
            applicationId "org.incoder.android"
            minSdkVersion 16
            // 正式环境域名 API 接口地址
            buildConfigField 'String', 'API', '"http://api.xxx.xxx/"'
            versionCode 2020122501
            versionName "2.1"
            // 指定产品变种
            dimension "minSDK"
        }
    }

    // 过滤指定产品变种（渠道，构建类型）
//    variantFilter { variant ->
//        def names = variant.flavors*.name
//        def isDebug = variant.buildType.debuggable
//        // To check for a certain build type, use variant.buildType.name == "<buildType>"
//        if (names.contains("rel") && isDebug) {
//            // Gradle ignores any variants that satisfy the conditions above.
//            setIgnore(true)
//        }
//    }

    // 多渠道配置
    productFlavors.all {
        flavor ->
            flavor.manifestPlaceholders = [CHANNEL_VALUE: name]
    }

    // 执行lint检查，有任何的错误或者警告提示，都会终止构建
    lintOptions {
        disable 'MissingTranslation', 'ExtraTranslation'
        // abortOnError一定要设为false，这样即使有报错也不会停止打包了
        abortOnError false
        // 在打包Release版本的时候进行检测,可以打开，这样报错还会显示出来
        checkReleaseBuilds false
    }

    dexOptions {
        jumboMode true
        javaMaxHeapSize "4g"
    }

    // 打包时的配置
    packagingOptions {
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/rxjava.properties'
    }

    aaptOptions.cruncherEnabled = false
    aaptOptions.useNewCruncher = false

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

// 项目依赖的包
dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.android.support:design:28.0.0'
    implementation 'com.android.support:support-v4:28.0.0'
    implementation 'com.android.support:support-v13:28.0.0'
    implementation 'com.android.support.constraint:constraint-layout:1.1.3'
    implementation 'com.android.support:support-vector-drawable:28.0.0'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
    testImplementation 'junit:junit:4.12'
}
```

关于 module 中的 build.gradle 配置文件中的各项已在示例中加入了注释说明，其中一些配置，这里再简单的说明下

### apply plugin

这里的 `apply plugin` 有两种模式：
1. com.android.application：表示这是一个应用程序模块
2. com.android.library：表示这是一个库模块

前者可以直接运行，后着是依附别的应用程序运行

### buildTypes

这里主要是生成安装文件的配置信息，一个 debug 类型，用于指定生成测试版安装文件配置，可忽略不写；另一个是 release，用于指定生成正式版安装文件的配置。

* minifyEnabled：是否对代码进行混淆，默认 false
* proguardFiles：指定混淆的规则文件，默认指定了 proguard-android.txt 文件和 proguard-rules.pro 文件。
  * proguard-android.txt：默认的混淆文件，里面定义了一些通用的混淆规则
  * proguard-rules.pro：位于当前项目的根目录下，可以在该文件中定义一些项目特有的混淆规则
* buildConfigField：可用于解决不同渠道不同的服务地址，或不同渠道 LOG 打印控制等
* debuggable：是否支持断点调试，release 默认为 false，debug 默认 true
* jniDebuggable：是否可以调试 NDK 代码，使用 lldb 进行 C 和 C++ 代码调试，release 默认为 false
* signingConfig：设置签名信息，通过 singingConfig.release 或 singingConfig.debug，配置相应的签名，但是添加此配置前需要先添加 singingConfig 闭包
* renderscriptDebuggable：是否开启渲染脚本，就是一些 C 写的渲染方法，默认为 false
* renderscriptOptimLevel：渲染等级，默认为 3
* zipAlignEnabled：是否对 apk 包执行 zip 对齐优化，减少 zip 体积，提高运行效率，release 和 debug 都默认 true
* pseudoLocalesEnabled：是否在 apk 中生成伪语言环境，帮助国际化，一般很少使用
* applicationIdSuffix：和 defaultConfig 中配置一样，指在 applicationId 中添加一个后缀
* versionNameSuffix：添加版本名称的后缀，一般使用较少

### productFlavors

这个配置主要是解决应用发布在不同应用市场，而需要对不同应用市场做一些不同配置，比如包名，应用名，以及一些统计，而需要不同渠道统计 ID 等

### packagingOptions

packagingOptions 常见的设置项有 exclude、pickFirst、doNotStrip、merge
1. exclude：过滤掉某些文件或者目录不添加到APK中，作用于APK，不能过滤aar和jar中的内容
   ```groovy
    packagingOptions {
        exclude 'META-INF/**'
        exclude 'lib/arm64-v8a/libmediaplayer.so'
    }
   ```
2. pickFirst：匹配到多个相同文件，只提取第一个。只作用于APK，不能过滤aar和jar中的文件
   ```groovy
    packagingOptions {
        pickFirst "lib/armeabi-v7a/libaaa.so"
        pickFirst "lib/armeabi-v7a/libbbb.so" 
    }
   ```
3. doNotStrip：可以设置某些动态库不被优化压缩
   ```groovy
    packagingOptions{
        doNotStrip "*/armeabi/*.so"
        doNotStrip "*/armeabi-v7a/*.so"
    }
   ```
4. merge：将匹配的文件都添加到APK中，和pickFirst有些相反，会合并所有文件
   ```groovy
    packagingOptions {
        merge '**/LICENSE.txt'
        merge '**/NOTICE.txt'
    }
   ```

## 统一版本

应用由多个 module 构成，而不同地方引用的包，需要做到全局的统一时，可以创建一个 `xxx.gradle` 的文件（这里的 xxx，自行取一个表明含义的内容即可），然后在使用的地方时，统一调用定义的版本即可，使用步骤如下

1. 创建 `xxx.gradle` 文件（一般放在项目的根目录，和顶级 build.gradle 文件在同一层级），并添加如下内容，可根据自身需要调整
    ```groovy
    ext {

        android = [
                compileSdkVersion: 29,
                buildToolsVersion: "29.0.2",
                minSdkVersion    : 19,
                targetSdkVersion : 29,
                versionCode      : 2020010102,
                versionName      : "0.1.0"
        ]

        version = [
                androidSupportSdkVersion: "29.0.0",
                retrofitSdkVersion      : "2.6.3",
                okhttpSdkVersion        : "4.3.0",
                dagger2SdkVersion       : "2.22.1",
                glideSdkVersion         : "4.9.0",
                butterknifeSdkVersion   : "10.2.1",
                rxlifecycle2SdkVersion  : "2.2.1",
                espressoSdkVersion      : "3.0.2",
                canarySdkVersion        : "1.5.4"
        ]

        // Android support 与 AndroidX support 对比
        // https://developer.android.google.cn/jetpack/androidx/migrate

        // support 库说明
        // https://developer.android.com/topic/libraries/support-library/features?hl=zh-cn
        dependencies = [
                // support
                "appcompat"                   : "androidx.appcompat:appcompat:1.1.0",
                "annotations"                 : "androidx.annotation:annotation:1.0.0",
                "cardview-v7"                 : "androidx.cardview:cardview:1.0.0",
                "constraint-layout"           : "androidx.constraintlayout:constraintlayout:1.1.3",
                "swiperefreshlayout"          : "androidx.swiperefreshlayout:swiperefreshlayout:1.0.0",
                "material"                    : "com.google.android.material:material:1.1.0",
                "viewpager"                   : "androidx.viewpager:viewpager:1.0.0",
                "recyclerview"                : "androidx.recyclerview:recyclerview:1.1.0",
                "vectordrawable"              : "androidx.vectordrawable:vectordrawable:1.1.0",
                "support-v4"                  : "androidx.legacy:legacy-support-v4:1.0.0",

                // network
                "retrofit"                    : "com.squareup.retrofit2:retrofit:${version["retrofitSdkVersion"]}",
                "retrofit-converter-gson"     : "com.squareup.retrofit2:converter-gson:${version["retrofitSdkVersion"]}",
                "retrofit-converter-simplexml": "com.squareup.retrofit2:converter-simplexml:${version["retrofitSdkVersion"]}",
                "retrofit-adapter-rxjava2"    : "com.squareup.retrofit2:adapter-rxjava2:${version["retrofitSdkVersion"]}",
                "okhttp3"                     : "com.squareup.okhttp3:okhttp:${version["okhttpSdkVersion"]}",
                "okhttp3-logging-interceptor" : "com.squareup.okhttp3:logging-interceptor:${version["okhttpSdkVersion"]}",
                "mockwebserver"               : "com.squareup.okhttp3:mockwebserver:${version["okhttpSdkVersion"]}",
                "glide"                       : "com.github.bumptech.glide:glide:${version["glideSdkVersion"]}",
                // （annotationProcessor）
                "glide-compiler"              : "com.github.bumptech.glide:compiler:${version["glideSdkVersion"]}",
                "glide-loader-okhttp3"        : "com.github.bumptech.glide:okhttp3-integration:${version["glideSdkVersion"]}",

                // view
                "butterknife"                 : "com.jakewharton:butterknife:${version["butterknifeSdkVersion"]}",
                "butterknife-compiler"        : "com.jakewharton:butterknife-compiler:${version["butterknifeSdkVersion"]}",
                "brvah"                       : "com.github.CymChad:BaseRecyclerViewAdapterHelper:2.9.49-androidx",
                "psid"                        : "com.oushangfeng:PinnedSectionItemDecoration:1.3.2-androidx",
                "material-dialogs"            : "com.afollestad.material-dialogs:core:2.8.1",
                "material-input"              : "com.afollestad.material-dialogs:input:2.8.1",
                "material-files"              : "com.afollestad.material-dialogs:files:2.8.1",
                "material-color"              : "com.afollestad.material-dialogs:color:2.8.1",
                "material-datetime"           : "com.afollestad.material-dialogs:datetime:2.8.1",
                "pickerview"                  : "com.contrarywind:Android-PickerView:4.1.8",
                "photoview"                   : "com.github.chrisbanes.photoview:library:2.0.0",
                "lottie"                      : "com.airbnb.android:lottie:3.0.1",
                "badge-view"                  : "q.rorbin:badgeview:1.1.3",

                // rx2
                "rxandroid2"                  : "io.reactivex.rxjava2:rxandroid:2.1.1",
                "rxjava2"                     : "io.reactivex.rxjava2:rxjava:2.2.16",
                // https://github.com/VictorAlbertos/RxCache
                "rxcache2"                    : "com.github.VictorAlbertos.RxCache:runtime:1.8.3-2.x",
                // https://github.com/tbruyelle/RxPermissions
                "rxpermissions2"              : "com.github.tbruyelle:rxpermissions:0.10.2",

                // tools（implementation）
                "dagger2"                     : "com.google.dagger:dagger:${version["dagger2SdkVersion"]}",
                "dagger2-android"             : "com.google.dagger:dagger-android:${version["dagger2SdkVersion"]}",
                "dagger2-android-support"     : "com.google.dagger:dagger-android-support:${version["dagger2SdkVersion"]}",
                "eventbus"                    : "org.greenrobot:eventbus:3.1.1",
                "gson"                        : "com.google.code.gson:gson:2.8.5",
                // https://projectlombok.org/setup/android
                "lombok"                      : "org.projectlombok:lombok:1.18.8",
                "multidex"                    : "com.android.support:multidex:1.0.3",
                "arouter-api"                 : "com.alibaba:arouter-api:1.4.1",
                "arouter-compiler"            : "com.alibaba:arouter-compiler:1.2.2",
                //（annotationProcessor）
                "dagger2-compiler"            : "com.google.dagger:dagger-compiler:${version["dagger2SdkVersion"]}",
                "dagger2-android-processor"   : "com.google.dagger:dagger-android-processor:${version["dagger2SdkVersion"]}",

                // test
                "junit"                       : "junit:junit:4.12",
                "androidJUnitRunner"          : "androidx.test.runner.AndroidJUnitRunner",
                "runner"                      : "androidx.test:runner:1.1.1",
                "espresso-core"               : "androidx.test.espresso:espresso-core:3.2.0",
                "espresso-contrib"            : "androidx.test.espresso:espresso-contrib:3.2.0",
                "espresso-intents"            : "androidx.test.espresso:espresso-intents:3.3.0",
                "canary-debug"                : "com.squareup.leakcanary:leakcanary-android:${version["canarySdkVersion"]}",
                "canary-release"              : "com.squareup.leakcanary:leakcanary-android-no-op:${version["canarySdkVersion"]}",
                "umeng-analytics"             : "com.umeng.analytics:analytics:6.0.1",

                // util
                // https://github.com/Blankj/AndroidUtilCode/blob/master/utilcode/README-CN.md
                "utilcode"                    : "com.blankj:utilcode:1.23.7",

                // help
                "logger"                      : "com.orhanobut:logger:2.2.0",
                // https://www.pgyer.com/doc/view/new_sdk_android_guide
                "pgy"                         : "com.pgyersdk:sdk:3.0.3",
                // SDK 包
                // https://bugly.qq.com/docs/release-notes/release-android-bugly/?v=20180709165613
                // https://jcenter.bintray.com/com/tencent/bugly/crashreport/
                "crashreport"                 : "com.tencent.bugly:crashreport:3.1.0",
                // 升级 SDK 包
                // https://bugly.qq.com/docs/release-notes/release-android-bugly/?v=20180709165613
                // https://jcenter.bintray.com/com/tencent/bugly/crashreport_upgrade/
                "bugly-crash-upgrade"         : "com.tencent.bugly:crashreport_upgrade:1.4.2",
                // NDK 动态库
                // https://bugly.qq.com/docs/release-notes/release-android-ndk/?v=20180709165613
                // https://jcenter.bintray.com/com/tencent/bugly/nativecrashreport/
                "bugly-ndk"                   : "com.tencent.bugly:nativecrashreport:3.7.1",

        ]
    }
    ```
2. 在顶级的 `build.gradle` 文件底部，表明添加对 `xxx.gradle` 的使用
   ```groovy
   apply from: "xxx.gradle"
   ```
3. 在 module 级别的 `build.gradle` 文件中，修改哪些固定写死的依赖版本
   ```groovy
   // 之前固定的版本
   minSdkVersion 19
   // 修改通过 xxx.gradle 中定义的版本
   minSdkVersion rootProject.ext.android["minSdkVersion"]
   ```

## 参考

1. [Android Gradle 插件版本说明](https://developer.android.google.cn/studio/releases/gradle-plugin?hl=zh_cn)
2. [配置构建](https://developer.android.google.cn/studio/build?hl=zh_cn)
3. [配置构建变体](https://developer.android.google.cn/studio/build/build-variants?hl=zh_cn#product-flavors)
