---
layout: post
title: YYWKWebView_evaluateJavaScriptFromString
date: 2018-05-02
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: YYWKWebView 在oc 执行js 的使用方法
---

# 前言


>* YYUIWebView.h
>
>```
- (void)evaluateJavaScriptFromString:(id)arg1 completionBlock:(CDUnknownBlockType)arg2;
>```

# YYUIWebView的例子


>* 执行js方法
><script src="https://gist.github.com/zhangkn/5b2a0994c064b79d4d2ceec8d1a4e13a.js"></script>

####  获取html内容

>* 获取html内容
><script src="https://gist.github.com/zhangkn/44615635ba9e0f8da899636dfee6d89a.js"></script>


>* 获取url 的正则
><script src="https://gist.github.com/zhangkn/bf22f38864d0dafbf2ba656ac875c773.js"></script>
>


# UIWebView 的例子

<script src="https://gist.github.com/zhangkn/cb676bf5f1abebc604a1f38a2d11fa92.js"></script>




# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost YYWKWebView_evaluateJavaScriptFromString YYWKWebView 在oc 执行js 的使用方法 -t iOS
> #原来""的参数，需要自己加上""
```

