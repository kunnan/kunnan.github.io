---
layout: post
title: respondsToSelector
date: 2018-06-20
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 处理不同版本的API
---


# NSStringFromSelector

>* NSStringFromSelector

```
            if([verifyLogic respondsToSelector:@selector(startWithVerifyContactWrap:opCode:parentView:fromChatRoom:)]){
                // 此方法已经被废弃 目前暂时考虑最新版，以后可以考虑适配就版本
                [verifyLogic startWithVerifyContactWrap:[NSArray arrayWithObject:wrap] opCode:3 parentView:[UIView new] fromChatRoom:NO];
                
                NSLog(@"verifyLogic :%@",NSStringFromSelector(@selector(startWithVerifyContactWrap:opCode:parentView:fromChatRoom:)));

            }else if([verifyLogic respondsToSelector:@selector(startForVerifyOK:parentView:)]){
                NSLog(@"verifyLogic :%@",NSStringFromSelector(@selector(startForVerifyOK:parentView:)));
                      
                [verifyLogic startForVerifyOK:wrap parentView:[UIView new]];
            }            
```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost respondsToSelector 处理不同版本的API -t objc
> #原来""的参数，需要自己加上""
```

