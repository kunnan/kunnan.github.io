---
layout: post
title: Modify_positioning
date: 2018-08-21
tag: tweak
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 修改定位 直接hook CLLocation
---



# # code

```
/**
 1. 修改定位; hook 原生API，直接替换为自己的即可
 */

#import <CaptainHook/CaptainHook.h>
#import "WechatPodForm.h"
#import <UIKit/UIKit.h>

CHDeclareClass(CLLocation);

CHOptimizedMethod0(self, CLLocationCoordinate2D, CLLocation, coordinate){
    CLLocationCoordinate2D coordinate = CHSuper(0, CLLocation, coordinate);
    if(pluginConfig.location.longitude || pluginConfig.location.latitude ){
        coordinate = pluginConfig.location;
    }
    return coordinate;
}

CHConstructor{
    CHLoadLateClass(CLLocation);
    CHClassHook(0, CLLocation, coordinate);
}

```

> * [DingtalkPod](https://github.com/deskOfDafa/DingtalkPod/blob/master/DingtalkPod/dingdingDylib.m)
>
>   * 功能介绍：修改钉钉位置打卡，调用 DingtalkPod 中 -(void)setLocation:(CLLocationCoordinate2D)location即可
>
>   * code: 主要还是hook原生的
>
>     ```
>     CHConstructor{
>         CHLoadLateClass(CLLocation);
>         CHClassHook(0, CLLocation, coordinate);
>     }
>     
>     ```
>

# See Also 

>* [WeChatLocationHook.m](https://github.com/zhangkn/WeChatPod/blob/master/WechatPod/Hook/WeChatLocationHook.m)
>* https://github.com/AloneMonkey/MonkeyDevSpecs#wechatpod
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Modify_positioning 修改定位 直接hook CLLocation -t tweak
> #原来""的参数，需要自己加上""
```

