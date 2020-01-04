---
title: Android 音频录制与播放
date: 2018-10-27 09:44:46
categories: Android
tag: [media]
---

上一篇主要介绍了音频相关的一些基础知识，本篇主要介绍在Android系统中如何进行音频的录制，播放

## 音频录制

Android SDK中提供了`AudioRecord`，`MediaRecorder`两个API经行音频的录制，具体的优缺点等如下：

* [AudioRecord](https://developer.android.google.cn/reference/android/media/AudioRecord) 『added in API level 3』(基于字节流录音)：  
    优点：可以实现语音的实时处理，进行边录边播，对音频的实时处理。   
    缺点：输出的是PCM的语音数据，如果保存成音频文件是不能被播放器播放的。要用到这个去进行处理。  
    适用场景：需要实时处理分析的录音场景等，如：会说话的汤姆猫『[AppStore](https://itunes.apple.com/cn/app/%E4%BC%9A%E8%AF%B4%E8%AF%9D%E7%9A%84%E6%B1%A4%E5%A7%86%E7%8C%AB/id377194688?mt=8) | [GooglePlay](https://play.google.com/store/apps/details?id=com.outfit7.talkingtom&hl=zh)』

<!-- more -->

* [MediaRecorder](https://developer.android.google.cn/reference/android/media/MediaRecorder) 『added in API level 1』(基于文件音视频录制)：  
    优点：封装度很高，操作简单，无需处理中间录制过程；录制的音频文件是经过压缩的，需要设置编码器；录制的音频文件可以使用系统自带的播放器播放  
    缺点：无法实现实时处理音频，输出的音频格式少。  
    适用场景：录制过程需要实时处理的场景等

## 音频播放
* [AudioTrack](https://developer.android.google.cn/reference/android/media/AudioTrack)『added in API level 3』：
AudioTrack 则更接近底层，提供了非常强大的控制能力，支持低延迟播放，适合流媒体和VoIP语音电话等场景

* [SoundPool](https://developer.android.google.cn/reference/android/media/SoundPool) 『added in API level 1』：  
    优点：主要用于播放一些较短的声音片段，支持从程序的资源或文件系统加载；CPU的资源占用量低、反应延迟小，并且可以加载多个音频到`SoundPool`中，通过资源ID来管理  
    缺点：SoundPool加载资源，最大只能申请 **1MB** 的内存控件，因此只能用来播放一些很短的声音片段  
    适用场景：播放短，反应要求高的音频

* [MediaPlayer](https://developer.android.google.cn/reference/android/media/MediaPlayer) 『added in API level 1』(基于字节流音视频播放)：  
    优点：支持本地，网络音频资源的播放   
    缺点：资源占用量较高、加载延迟时间较长；不支持多个音频同时播放等  
    适用场景：播放长音频

>Google官方给出了[兼容支持](https://developer.android.google.cn/guide/topics/media/media-formats#audio-formats)

## AudioRecord

### 录制流程

1. 构造一个`AudioRecord`对象，其中需要的最小音频缓存`buffer`大小可以通过`getMinBufferSize()`方法得到，如果`buffer`容量过小，将导致对象构造失败
2. 初始化一个`buffer`，该`buffer` 大于等于`AudioRecord`对象用于写音频数据的`buffer`大小
3. 开始录音
4. 创建一个数据流，一边从`AudioRecord`中读取音频数据到初始化的`buffer`，一边将`buffer`中的数据导入数据流
5. 关闭数据流
6. 停止录音

### 参数配置

* audioSource ：音频采集的输入源
    * DEFAULT（默认）
    * VOICE_RECOGNITION（用于语音识别，等同于DEFAULT）
    * MIC（由手机麦克风输入）
    * VOICE_COMMUNICATION（用于VoIP应用）
* sampleRateInHz：采样率
    目前44100Hz是唯一可以保证兼容所有Android手机的采样率
* channelConfig：通道数的配置
    * CHANNEL_IN_MONO：单通道
    * CHANNEL_IN_STEREO：双通道
* audioFormat：数据位宽
    * ENCODING_PCM_8BIT：8bit
    * ENCODING_PCM_16BIT：16bit
* bufferSizeInBytes：AudioRecord 内部的音频缓冲区的大小，该缓冲区的值不能低于一帧“音频帧”（Frame）的大小


### 示例代码
```java
public class AudioCapturer {

    private static final String TAG = "AudioCapturer";
	
    private static final int DEFAULT_SOURCE = MediaRecorder.AudioSource.MIC;
    private static final int DEFAULT_SAMPLE_RATE = 44100;
    private static final int DEFAULT_CHANNEL_CONFIG = AudioFormat.CHANNEL_IN_MONO;
    private static final int DEFAULT_AUDIO_FORMAT = AudioFormat.ENCODING_PCM_16BIT;

    private AudioRecord mAudioRecord;
    private int mMinBufferSize = 0;
	
    private Thread mCaptureThread; 	
    private boolean mIsCaptureStarted = false;
    private volatile boolean mIsLoopExit = false;

    private OnAudioFrameCapturedListener mAudioFrameCapturedListener;

    public interface OnAudioFrameCapturedListener {
        public void onAudioFrameCaptured(byte[] audioData);
    }	

    public boolean isCaptureStarted() {		
        return mIsCaptureStarted;
    }

    public void setOnAudioFrameCapturedListener(OnAudioFrameCapturedListener listener) {
        mAudioFrameCapturedListener = listener;
    }

    public boolean startCapture() {
        return startCapture(DEFAULT_SOURCE, DEFAULT_SAMPLE_RATE, DEFAULT_CHANNEL_CONFIG,
            DEFAULT_AUDIO_FORMAT);
    }

    public boolean startCapture(int audioSource, int sampleRateInHz, int channelConfig, int audioFormat) {

        if (mIsCaptureStarted) {
            Log.e(TAG, "Capture already started !");
            return false;
        }
    
        mMinBufferSize = AudioRecord.getMinBufferSize(sampleRateInHz,channelConfig,audioFormat);
        if (mMinBufferSize == AudioRecord.ERROR_BAD_VALUE) {
            Log.e(TAG, "Invalid parameter !");
            return false;
        }
        Log.d(TAG , "getMinBufferSize = "+mMinBufferSize+" bytes !");
		
        mAudioRecord = new AudioRecord(audioSource,sampleRateInHz,channelConfig,audioFormat,mMinBufferSize);				
        if (mAudioRecord.getState() == AudioRecord.STATE_UNINITIALIZED) {
    	    Log.e(TAG, "AudioRecord initialize fail !");
	    return false;
        }		

        mAudioRecord.startRecording();

        mIsLoopExit = false;
        mCaptureThread = new Thread(new AudioCaptureRunnable());
        mCaptureThread.start();

        mIsCaptureStarted = true;

        Log.d(TAG, "Start audio capture success !");

        return true;
    }

    public void stopCapture() {

        if (!mIsCaptureStarted) {
            return;
        }

        mIsLoopExit = true;		
        try {
            mCaptureThread.interrupt();
            mCaptureThread.join(1000);
        } 
        catch (InterruptedException e) {		
            e.printStackTrace();
        }

        if (mAudioRecord.getRecordingState() == AudioRecord.RECORDSTATE_RECORDING) {
            mAudioRecord.stop();						
        }

        mAudioRecord.release();		
	
        mIsCaptureStarted = false;
        mAudioFrameCapturedListener = null;

        Log.d(TAG, "Stop audio capture success !");
    }

    private class AudioCaptureRunnable implements Runnable {		
	
        @Override
        public void run() {

            while (!mIsLoopExit) {

                byte[] buffer = new byte[mMinBufferSize];

                int ret = mAudioRecord.read(buffer, 0, mMinBufferSize);				
                if (ret == AudioRecord.ERROR_INVALID_OPERATION) {
                    Log.e(TAG , "Error ERROR_INVALID_OPERATION");
                } 
                else if (ret == AudioRecord.ERROR_BAD_VALUE) {
                    Log.e(TAG , "Error ERROR_BAD_VALUE");
                } 
                else { 
                    if (mAudioFrameCapturedListener != null) {
                        mAudioFrameCapturedListener.onAudioFrameCaptured(buffer);
                    }   
                    Log.d(TAG , "OK, Captured "+ret+" bytes !");
                }														
            }		
        }    
    }
}
```
## AudioTrack

### 播放流程

1. 配置参数，初始化内部的音频播放缓冲区到，如果`buffer`容量过小，将导致对象构造失败
2. 开始播放
3. 需要一个线程，不断地向 AudioTrack 的缓冲区`写入`音频数据，注意，这个过程一定要及时，否则就会出现`underrun`的错误，该错误在音频开发中比较常见，意味着应用层没有及时地“送入”音频数据，导致内部的音频播放缓冲区为空
4. 停止播放，释放资源

### 参数配置
* streamType：当前应用使用的哪一种音频管理策略
当系统有多个进程需要播放音频时，这个管理策略会决定最终的展现效果
    * STREAM_VOCIE_CALL：电话声音
    * STREAM_SYSTEM：系统声音
    * STREAM_RING：铃声
    * STREAM_MUSCI：音乐声
    * STREAM_ALARM：警告声
    * STREAM_NOTIFICATION：通知声
* sampleRateInHz：采样率
采样率的取值范围必须在 4000Hz～192000Hz 之间
* channelConfig：通道数的配置
    * CHANNEL_IN_MONO：单通道
    * CHANNEL_IN_STEREO：双通道
* audioFormat：数据位宽
    * ENCODING_PCM_8BIT：8bit
    * ENCODING_PCM_16BIT：16bit
* bufferSizeInBytes：配置的是 AudioTrack 内部的音频缓冲区的大小，该缓冲区的值不能低于一帧“音频帧”（Frame）的大小
* mode：AudioTrack 播放模式
    * MODE_STATIC
        static：一次性将所有的数据都写入播放缓冲区，简单高效，通常用于播放铃声、系统提醒的音频片段
    * MODE_STREAM
        streaming：按照一定的时间间隔不间断地写入音频数据，理论上它可用于任何音频播放的场景

### 示例代码

```java
public class AudioPlayer {
    
    private static final String TAG = "AudioPlayer";

    private static final int DEFAULT_STREAM_TYPE = AudioManager.STREAM_MUSIC;
    private static final int DEFAULT_SAMPLE_RATE = 44100;
    private static final int DEFAULT_CHANNEL_CONFIG = AudioFormat.CHANNEL_IN_STEREO;
    private static final int DEFAULT_AUDIO_FORMAT = AudioFormat.ENCODING_PCM_16BIT;
    private static final int DEFAULT_PLAY_MODE = AudioTrack.MODE_STREAM;
            
    private boolean mIsPlayStarted = false;
    private int mMinBufferSize = 0;
    private AudioTrack mAudioTrack;  
    
    public boolean startPlayer() {
        return startPlayer(DEFAULT_STREAM_TYPE,DEFAULT_SAMPLE_RATE,DEFAULT_CHANNEL_CONFIG,DEFAULT_AUDIO_FORMAT);
    }
    
    public boolean startPlayer(int streamType, int sampleRateInHz, int channelConfig, int audioFormat) {
        
        if (mIsPlayStarted) {
            Log.e(TAG, "Player already started !");
            return false;
        }
        
        mMinBufferSize = AudioTrack.getMinBufferSize(sampleRateInHz,channelConfig,audioFormat);
        if (mMinBufferSize == AudioTrack.ERROR_BAD_VALUE) {
            Log.e(TAG, "Invalid parameter !");
            return false;
        }
        Log.d(TAG , "getMinBufferSize = "+mMinBufferSize+" bytes !");
        
        mAudioTrack = new AudioTrack(streamType,sampleRateInHz,
                            channelConfig,audioFormat,mMinBufferSize,DEFAULT_PLAY_MODE);

        if (mAudioTrack.getState() == AudioTrack.STATE_UNINITIALIZED) {
            Log.e(TAG, "AudioTrack initialize fail !");
            return false;
        }            
        
        mIsPlayStarted = true;
        
        Log.d(TAG, "Start audio player success !");
        
        return true;
    }
    
    public int getMinBufferSize() {
        return mMinBufferSize;
    }
    
    public void stopPlayer() {
        
        if (!mIsPlayStarted) {
            return;
        }
        
        if (mAudioTrack.getPlayState() == AudioTrack.PLAYSTATE_PLAYING) {
            mAudioTrack.stop();                        
        }
        
        mAudioTrack.release();
        mIsPlayStarted = false;
           
        Log.d(TAG, "Stop audio player success !");
    }
    
    public boolean play(byte[] audioData, int offsetInBytes, int sizeInBytes) {
        
        if (!mIsPlayStarted) {
            Log.e(TAG, "Player not started !");
            return false;
        }
        
        if (sizeInBytes < mMinBufferSize) {
            Log.e(TAG, "audio data is not enough !");
            return false;
        }
        
        if (mAudioTrack.write(audioData,offsetInBytes,sizeInBytes) != sizeInBytes) {                
            Log.e(TAG, "Could not write all the samples to the audio device !");
        }                                   
                                                   
        mAudioTrack.play();
        
        Log.d(TAG , "OK, Played "+sizeInBytes+" bytes !");
        
        return true;
    }
}
```

## MediaRecorder

![mediarecorder](https://developer.android.google.cn/images/mediarecorder_state_diagram.gif)

如上所示表述整个MediaRecorder的整个生命过程，可以看出初始化之后，在任意的状态下调用`reset()`方法均可以回到MediaRecorder刚刚初始化完成的状态

## MediaPlayer

![mediaplayer](https://developer.android.google.cn/images/mediaplayer_state_diagram.gif)

### MediaPlayer 工作流程

1. 创建一个MediaPlayer对象
2. 调用setDataSource()方法，设置音频文件的路径
3. 接着调用prepare()方法，使MediaPlayer进入的准备状态
4. 调用start()方法，开始播放音频『pause()方法表示：暂停播放』

### MediaPlayer常用的控制方法

|    方法名    |    功能描述    |
| ---------- | --- |
| setDataSource() |  设置要播放的音频文件的位置 |
| prepare()       |  在开始播放之前调用这个方法完成准备工作 |
| start()         |  开始或继续播放音频 |
| pause()         |  暂停播放音频 |
| reset()         |  将MediaPlayer对象重置到刚刚创建的状态 |
| seekTo()        |  从指定位置开始播放音频 |
| stop()          |  停止播放音频。调用这个方法后的MediaPlayer对象无法再播放音频 |
| release()       |  释放掉与MediaPlayer对象相关的资源 |
| isPlaying()     |  判断当前MediaPlayer是否正在播放音频 |
| getDuration()   |  获取站如的音频文件的时长 |

### 注意事项

1. 在使用`star()`播放流媒体之前，需要装载流媒体资源。这里最好使用`prepareAsync()`异步的方式装载流媒体资源，在使用`prepareAsync()`异步加载时，为避免还没有装载完就调用了`start()`而保存，需要绑定`MediaPlayer.setOnPreparedListener()`事件，它将在异步装在完成后回调   
原因：流媒体资源的装载是会消耗系统资源，在一些硬件不理想的设备上，如果使用`prepare()`同步的方式装载资源，可能会造成UI界面卡顿，其次避免装载超时而引发`ANR`等问题
2. 使用完MediaPlayer需要回收资源。MediaPlayer时很消耗系统资源的，所以在使用完MediaPlayer，及时主动回收资源
3. 对于单曲循环之类的操作，除了使用`setLooping()`方法设置之外，还可以为MediaPlayer注册回调函数，`MediaPlayer.setOnCompletionListener()`，它会在MediaPlayer播放完被回调
4. 由于无法确保播放的流媒体是完整（中间有错误），我们需要处理这个错误，否则会影响用户体验。可以在MediaPlayer中注册`setOnErrorListener()`错误回调，一般重新播放或者播放下一个流媒体

## 跨平台

关于音频编解码在各平台上的情况如下
![wiki-ecode](https://res.cloudinary.com/incoder/image/upload/v1541055152/blog/android-audio.png)

从上图可知，[AAC](https://zh.wikipedia.org/wiki/%E9%80%B2%E9%9A%8E%E9%9F%B3%E8%A8%8A%E7%B7%A8%E7%A2%BC)，[FLAC](https://zh.wikipedia.org/wiki/FLAC)，[MP3](https://zh.wikipedia.org/wiki/MP3)三种编码是全平台支持的音频编码方式（或音频压缩方式），注意编码方式并不是文件格式即文件的扩展名

* AAC 主要扩展名
    * `.aac`
    * `.mp4`
    * `.m4a`
* FLAC 扩展名
    * `.flac`
* MP3 扩展名
    * `.mp3`

## 总结

* 音频的录制，Android SDK提供了两套音频采集的API，分别是：`MediaRecorder`和`AudioRecord`，前者是一个更加上层一点的API，它可以直接把手机麦克风录入的音频数据进行编码压缩（如：`AMR`,`OGG`等）并存储成文件，而后者则更接近底层，能够更加自由灵活的控制，可以得到原始的一帧帧`PCM`音频数据
* 如果要简单的进行音频的采集，录制成音频文件，则推荐适用`MediaRecorder`，而如果需要对音频做进一步的算法处理，或者采用第三方的编码库进行压缩、以及网络传输等应用，则建议适用`AudioRecord`
* `MediaRecorder`底层的实现也是调用了`AudioRecord`与`Android Framework` 层的`AudioFlinger`进行交互

> 关于音视频相关的资料参差不齐，目前尚未有大量相关专门的书籍来介绍该领域的图书或者易懂视频，很多情况需要根据所处应用场景灵活应变。
推荐刚刚发行的一本关于音频方面的图书[《Android音视频开发》](https://item.jd.com/35027062396.html)
推荐国内比较专业音视频方面相关的介绍[《雷霄骅的专栏》](http://blog.csdn.net/leixiaohua1020)
## 附录

* [音频编码格式的比较](https://zh.wikipedia.org/wiki/%E9%9F%B3%E9%A2%91%E7%BC%96%E7%A0%81%E6%A0%BC%E5%BC%8F%E7%9A%84%E6%AF%94%E8%BE%83)
* [第一行代码](https://book.douban.com/subject/26915433)
* [Android MediaRecorder架构详解](http://www.isclab.org.cn/archives/2014/12/2946.html)
* [参考代码](https://github.com/googlesamples/android-MediaRecorder)
* [浏览器引擎](https://zh.wikipedia.org/wiki/%E6%8E%92%E7%89%88%E5%BC%95%E6%93%8E)
* [主流浏览器内核介绍](https://www.cnblogs.com/zichi/p/5116764.html)
* [腾讯X5内核介绍](https://x5.tencent.com/tbs/product/tbs.html)
* [Android 音视频开发学习思路](http://www.cnblogs.com/renhui/p/7452572.html)