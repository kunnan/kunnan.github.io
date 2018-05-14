---
layout: post
title: CFNotificationCenterAddObserver
date: 2018-05-14
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: CFNotificationCenterAddObserver 的使用
---


# CFNotificationCenterAddObserver

>* `#include <CoreFoundation/CFNotificationCenter.h>`
><script src="https://gist.github.com/zhangkn/74a599e16e2680d03f07baecb15d782b.js"></script>



>* CFNotificationCenterAddObserver
>```
>CF_EXPORT void CFNotificationCenterAddObserver(CFNotificationCenterRef center, const void *observer, CFNotificationCallback callBack, CFStringRef name, const void *object, CFNotificationSuspensionBehavior suspensionBehavior);
>```
>


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost CFNotificationCenterAddObserver CFNotificationCenterAddObserver 的使用 -t objc
> #原来""的参数，需要自己加上""
```

