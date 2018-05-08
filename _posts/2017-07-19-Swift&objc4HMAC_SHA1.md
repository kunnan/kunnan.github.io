---
layout:     post
title:      Swift&objc4HMAC_SHA1
subtitle:   swift中利用HMAC的SHA1对文本进行加密
date:       2017-07-19
author:     
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - Swift
    - objc
---

# 前言

>* HMAC是密钥相关的哈希运算消息认证码（Hash-based Message Authentication Code）。
>```
> HMAC运算利用哈希算法，以一个密钥和一个消息为输入，生成一个消息摘要作为输出。也就是说HMAC通过将哈希算法(SHA1, MD5)与密钥进行计算生成摘要。
> ```

# Objectice-C

>*  [objc 中，使用的 HMAC 和 SHA1 进行加密:KNCCHmac/KNCCHmac/KNCCHmacTool.m](https://github.com/kunnan/KNCCHmac/blob/master/KNCCHmac/KNCCHmacTool.m)


# swift


####  使用

```swift
// 使用HMAC和SHA加密
        NSLog("%@", "kn".hmac(algorithm: HMACAlgorithm.SHA1, key: "kn"))//kCCHmacAlgSHA1 musksf4d0ewfocjWO3X2nr5w9uA=
```

####  代码

>* 桥接文件`xxx-Bridging-Header`中 `#import <CommonCrypto/CommonHMAC.h>`
>```
>只要新增一个分类UIView (test)，Xcode 就会帮你自动创建Bridging-Header.h
>```

>* [KNCommonHMAC/KNCommonHMAC/CommonHMACStringExtension.swift](https://github.com/kunnan/KNCommonHMAC/blob/master/KNCommonHMAC/CommonHMACStringExtension.swift)
><script src="https://gist.github.com/zhangkn/502a512ccde770cdff04008927061853.js"></script>
>

# see also

>* [oauthconsumer/Crypto/Base64Transcoder.c](https://github.com/iossrc/oauthconsumer)
>```
>https://github.com/iossrc/oauthconsumer/blob/master/Crypto/Base64Transcoder.c
>OAuthConsumer.h
>```
