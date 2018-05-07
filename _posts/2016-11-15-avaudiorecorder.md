---
layout:     post
title:      avaudiorecorder
subtitle:   avaudiorecorder的averagePowerForChannel:测噪音
date:       2016-11-15
author:    
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
---


# 前言


>* 分贝的定义如下：
><script src="https://gist.github.com/zhangkn/27286979de33f00d9228483b1fcb145e.js"></script>
>


# iOS测噪音原理

>* iOS设备测量噪音原理：调用系统麦克风，根据麦克风输入强度计算转化为对应的dB值。


>* [iOS的`AVFoundation`框架中有一个`AVAudioRecorder`类专门处理录音操作](https://developer.apple.com/documentation/avfoundation/avaudiorecorder)
><script src="https://gist.github.com/zhangkn/86712f51dd0a14377314cba8de8bf5b6.js"></script>


# [code](https://github.com/kunnan/KNAVAudioRecorder/blob/master/KNAVAudioRecorder/ViewController.m)

>*  The app's Info.plist must contain an NSMicrophoneUsageDescription key with a string value explaining to the user how the app uses this data.
>


# see also

>* [iOS开发系列--音频播放、录音、视频播放、拍照、视频录制](http://www.cnblogs.com/kenshincui/p/4186022.html)

