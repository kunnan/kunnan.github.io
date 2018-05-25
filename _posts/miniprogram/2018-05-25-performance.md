---
layout: post
title: performance
date: 2018-05-25
tag: miniprogram
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: tips&tools
---


# setData

#### 工作原理


小程序的视图层目前使用 WebView 作为渲染载体，而逻辑层是由独立的 JavascriptCore 作为运行环境。

>* 视图层和逻辑层的数据传输，实际上通过两边提供的 evaluateJavascript 所实现。

>```
>即用户传输的数据，需要将其转换为字符串形式传递，同时把转换后的数据内容拼接成一份 JS 脚本，再通过执行 JS 脚本的形式传递到两边独立环境。
>```

# 图片对内存的影响


在 iOS 上，小程序的页面是由多个 WKWebView 组成的，在系统内存紧张时，会回收掉一部分 WKWebView.
```
大图片和长列表图片的使用会引起 WKWebView 的回收
```

# 控制代码包内图片资源


小程序代码包经过编译后，会放在微信的 CDN 上供用户下载，CDN 开启了 GZIP 压缩，所以用户下载的是压缩后的 GZIP 包，其大小比代码包原体积会更小。

```
GZIP 对基于文本资源的压缩效果最好，在压缩较大文件时往往可高达 70%-80% 的压缩率，而如果对已经压缩的资源（例如大多数的图片格式）则效果甚微
```

# 及时清理没有使用到的代码和资源

目前小程序打包是会将工程下所有文件都打入代码包内，也就是说，这些没有被实际使用到的库文件和资源也会被打入到代码包里，从而影响到整体代码包的大小。


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost performance tips&tools -t miniprogram
> #原来""的参数，需要自己加上""
```

