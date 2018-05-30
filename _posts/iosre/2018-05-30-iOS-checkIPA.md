---
layout: post
title: iOS-checkIPA
date: 2018-05-30
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Scans an IPA file and parses its Info.plist and embedded.mobileprovision files. Performs checks of expected key/value relationships and displays the results.
---


# KNiOS-checkIPA

>* [/Users/devzkn/code/iosre/iOS-checkIPA](https://github.com/kunnan/KNiOS-checkIPA)

```sh
 /usr/bin/python checkipa -i /Users/devzkn/Desktop/Payload.ipa 
```

```
Distribution: code signing Entitlements 'get-task-allow' value is set to YES; should be NO
```

# 知识补充

>* 将app 转化为ipa

```
打包： Payload文件夹放.app 文件；压缩Payload,修改后缀名为.ipa;
```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iOS-checkIPA Scans an IPA file and parses its Info.plist and embedded.mobileprovision files. Performs checks of expected key/value relationships and displays the results. -t iosre
> #原来""的参数，需要自己加上""
```

