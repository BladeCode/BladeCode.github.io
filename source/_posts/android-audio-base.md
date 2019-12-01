---
title: Android 音频基础知识
date: 2018-10-26 10:14:20
categories: Android
tag: [media]
---

关于音频技术是一门庞大且很专业的学术，这里不会阐述该知识的底层原理知识（比如：声音的原理，音波的正弦平面波合成等等），主要介绍音频相关的一些基本的知识概念，以及在实际开发过程中需要掌握关键API等。

## 声音

"声音是振动产生的`声波`，通过`介质`（`气体`，`固体`，`液体`）传播并能被人或动物`听觉器官`所感知的`波动`现象"。声音的频率一般以[赫兹](https://zh.wikipedia.org/wiki/%E8%B5%AB%E5%85%B9)表示，记为`Hz`，指每秒周期性震动的次数

<!-- more -->

![trasound_range_diagram](https://res.cloudinary.com/incoder/image/upload/v1541400788/blog/trasound_range_diagram.png)
>图片来自[Wikipedia](https://zh.wikipedia.org/wiki/%E5%A3%B0%E9%9F%B3)

* 红：次声波（由火山爆发、龙卷风、雷暴、台风等许多灾害性事件发生前都会产生出次声波，人们就可以利用这种前兆来预报灾害事件的发生）
* 蓝：可听声波（20~20000Hz）
* 绿：超声波（广泛应用于工业、军事、医疗等行业。在工业上，常用超声波来清洗精密零件，原理是利用超声波在清洗液中产生震荡波，使清洗液产生瞬间的小气泡，从而冲洗零件的每个角落）

## 音频开发应用场景

* 音频播放器，录音机
* 语音电话
* 音视频监控
* 音视频直播
* 音视频编辑/处理软件
* 蓝牙耳机/音响等

## 音频开发具体内容

* [音频采集/播放](https://www.incoder.org/2018/10/27/android-audio/)
* 音频算法处理（去噪，静音检测，回声消除，音效处理，功放/增强，混音/分离，等等）
* [音频的编解码和格式转换](https://www.incoder.org/2018/11/07/android-audio-convert/)
* 音频传输协议的开发（[SIP](https://zh.wikipedia.org/wiki/%E4%BC%9A%E8%AF%9D%E5%8F%91%E8%B5%B7%E5%8D%8F%E8%AE%AE)，[A2DP](https://zh.wikipedia.org/wiki/%E8%97%8D%E7%89%99%E8%A6%8F%E7%AF%84#%E8%97%8D%E7%89%99%E7%AB%8B%E9%AB%94%E8%81%B2%E9%9F%B3%E8%A8%8A%E5%82%B3%E8%BC%B8%E8%A6%8F%E7%AF%84%EF%BC%88A2DP%EF%BC%89)，[AVRCP](https://zh.wikipedia.org/wiki/%E8%97%8D%E7%89%99%E8%A6%8F%E7%AF%84#%E9%9F%B3%E9%A2%91%EF%BC%8F%E8%A7%86%E9%A2%91%E8%BF%9C%E7%A8%8B%E6%8E%A7%E5%88%B6%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%EF%BC%88AVRCP%EF%BC%89)，等等）
    * SIP（Session Initiation Protocol：会话发起协议）：一个由IETF MMUSIC工作组开发的协议，作为标准被提议用于建立，修改和终止包括视频，语音，即时通信，在线游戏和虚拟现实等多种多媒体元素在内的交互式用户会话
    * A2DP（Advance Audio Distribution Profile：蓝牙立体声音频传输规范）：规定了使用蓝牙异步传输信道方式，传输高质量音乐文件数据的协议堆栈软件和使用方法，基于该协议就能通过以蓝牙方式传输高质量的立体声音乐
    * AVRCP（Audio Video Remote Control Profile：音频／视频远程控制配置文件）：用于提供控制 TV、Hi-Fi 设备等的标准接口。此配置文件用于许可单个远程控制设备。

## 音频基础知识

声音经过麦克风采集后，得到是模拟信号，接着我们需要用程序将采集得到模拟型号，进行转换得到数字信号，这样我们才可以存储，交换等

>关于声音信息得到模拟信号的转换，我们一般是无需关心，设备的麦克风这些都已经帮我们转换好了，我们需要关心的是从麦克风得到的模拟信号，如何去转换为数字信号，最终保存为音频文件

### 模拟信号转数字信号

模拟信号一般通过[PCM（Pulse-code modulation：脉冲编码调制）](https://zh.wikipedia.org/wiki/%E8%84%88%E8%A1%9D%E7%B7%A8%E7%A2%BC%E8%AA%BF%E8%AE%8A)方法转换为数字信号

#### 转换步骤

1. [采样](https://zh.wikipedia.org/wiki/%E5%8F%96%E6%A8%A3)：将一段时间内的连续信号转为离散信号
    * [模拟信号](https://zh.wikipedia.org/wiki/%E8%BF%9E%E7%BB%AD%E4%BF%A1%E5%8F%B7)本身是一种连续信号，它在一定的时间范围内可以有无限多个不同的取值
    * [数值信号](https://zh.wikipedia.org/wiki/%E7%A6%BB%E6%95%A3%E4%BF%A1%E5%8F%B7)指在取值上是离散的，不连续的信号
2. 量化：值采样得到后的数据，我们用多少位的二进制数字来表示声音的振幅
3. [编码](https://zh.wikipedia.org/wiki/%E8%AA%9E%E9%9F%B3%E7%B7%A8%E7%A2%BC)：将采样量化后的数据按照一定的格式进行记录

#### PCM

音频编码最多只能做到无限接近，至少目前的技术只能这样，相对自然界的信号，任何数字音频编码方式都是有损，因为无法完全还原。在计算机应用中，能够达到最高保真的就是PCM编码，因此PCM约定俗成了无损编码（PCM代表了数字音频中最佳的保真水平，并不意味着PCM就能够确保信号绝对保真，PCM也只能做到最大程度的无限接近）

经过采集和量化后的声音信号已经是数字形式了，但是为了便于计算机的存储，处理，传输，还必须按照一定的要求进行数据`压缩`和`编码`

##### 压缩
一种音频文件格式可以支持多种编码，例如AVI文件格式，但多数的音频文件仅支持一种音频编码

主要的音频文件格式：

* 无损格式，例如：[WAV](https://zh.wikipedia.org/wiki/WAV)，[FLAC](https://zh.wikipedia.org/wiki/FLAC)，[APE](https://zh.wikipedia.org/wiki/Monkey%27s_Audio)，[ALAC](https://zh.wikipedia.org/wiki/Apple_Lossless)，[WavPack(WV)](https://zh.wikipedia.org/wiki/WavPack)
* 有损格式，例如：[MP3](https://zh.wikipedia.org/wiki/MP3)，[AAC](https://zh.wikipedia.org/wiki/%E9%80%B2%E9%9A%8E%E9%9F%B3%E8%A8%8A%E7%B7%A8%E7%A2%BC)，[Ogg Vorbis](https://zh.wikipedia.org/wiki/Vorbis)，[Opus](https://zh.wikipedia.org/wiki/Opus_%28%E9%9F%B3%E9%A2%91%E6%A0%BC%E5%BC%8F%29)

##### 编码

根据编码方式的不同，音频编码技术分为三种

* 波形编码：音质质量高，编码速率也很高。脉冲编码调变(PCM)、自适应增量调制( ADM )、Adaptive( ADPCM )等都属于该类编码器。
* 参数编码：音质质量低，编码速率也很低
* 混合编码：音质和速率介于波形编码，参数编码之间

>为什么音频需要编码
1. PCM所量化得到的数据是原始无损的数据，文件很大，不利于传播，存储等
2. 如果都是未压缩的文件，那么基本无法做到差异化即部分需要知识产权保护的组织或机构等

## 音频开发中重要参数

### [采样率（samplerate）](https://zh.wikipedia.org/wiki/%E9%87%87%E6%A0%B7%E7%8E%87)

指每秒从连续信号中提取并组成离散信号的采样个数，也就是1S内，对模拟信号进行多少次采样；采样频率越高，说明采样点之间越密集，记录这段音频所用的数据量就越大，因此音质也就越好

>[为什么通用的采样率是44.1kHz？](https://www.zhihu.com/question/22027722)

### 量化精度（位宽）

用二进位来表示每一个采样值，也称为量化位数，声音信号的量化位数一般是4，6，8，12或16 bits.

这个数值的数据类型大小可以是：4bit，8bit，16bit，32bit等等，位数越多，表示的就越精细，声音的质量也就越好，当然文件大小也会成倍增大

### 声道数（channels）

由于音频的采集和播放是可以叠加的，因此，可以同时从多个音频源采集声音，并分别输出到不同的扬声器，故声道数一般表示声音录制时的音源数量或回放时相应的扬声器数量

* 单声道（Mono）：1
* 双声道（Stereo）：2

### 比特率

比特率是音频文件每秒占据的字节数（比特数）

比特率规定适用“比特每秒”（`bit/s`或`bps`）为单位，其中`ps`指的是`/s`，即每秒。

通常我们在音乐播放软件中看到的音乐质量『标准(128kbit/s)，较高(198kbit/s)，极高(320kbit/s)』表述的即比特率  

### 音频帧（frame）

视频每一帧就是一张图像，而音频数据是流式，本身没有明确的一帧帧的概念，在实际的应用中，为了音频算法处理/传输的方便，一般约定俗称2.5ms~60ms为单位的数据量为一帧音频。

### 理论音频的大小

假设某通道的音频信号是采样率为8kHz，位宽为16bit，20ms一帧，双通道，则一帧音频数据的大小为：
```java
# 一帧音频的大小
int size = 8000 x 16bit x 0.02s  x 2 = 5120 bit = 640 byte;
```

## 音频处理开源项目

### VoIP相关
基于IP的语音传输（英语：Voice over Internet Protocol，缩写为VoIP）是一种语音通话技术，经由网际协议（IP）来达成语音通话与多媒体会议，也就是经由互联网来进行通信。其他非正式的名称有IP电话（IP telephony）、互联网电话（Internet telephony）、宽带电话（broadband telephony）以及宽带电话服务（broadband phone service）。
* imsdroid
* sipdroid
* csipsimple
* linphone
* WebRTC

### 算法相关
* ffmpeg
* speex

### 其他
MP3编码库
* [Lame](https://sourceforge.net/projects/lame)

## Android提供相关API

* 音频采集：MediaRecoder，AudioRecord
* 音频播放：SoundPool，MediaPlayer，AudioTrack
* 音频编解码：MediaCodec
* NDK API：OpenSL ES

## 附录
  
* [语音编码](https://zh.wikipedia.org/wiki/%E8%AA%9E%E9%9F%B3%E7%B7%A8%E7%A2%BC)
* [高级音频编码 ● AAC](https://zh.wikipedia.org/wiki/%E9%80%B2%E9%9A%8E%E9%9F%B3%E8%A8%8A%E7%B7%A8%E7%A2%BC)
* [音频技术可以延展众多应用场景](https://yq.aliyun.com/articles/628109)