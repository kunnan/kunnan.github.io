---
layout: post
title: CYListenServer
date: 2018-07-01
tag: Cycript
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 开发集成cycript
---



# 集成步骤



> * Constructor 中处理集成cy,特定条件执行`CYListenServer ()`来设置Cycript默认端口是`6666`
>
>   ```
>   #define CHConstructor static __attribute__((constructor)) void CHConcat(CHConstructor, __LINE__)()
>   CHConstructor{
>       NSLog(INSERT_SUCCESS_WELCOME);
>       
>       [[NSNotificationCenter defaultCenter] addObserverForName:UIApplicationDidFinishLaunchingNotification object:nil queue:[NSOperationQueue mainQueue] usingBlock:^(NSNotification * _Nonnull note) {
>           
>   #ifndef __OPTIMIZE__
>           CYListenServer(6666);
>   
>           MDCycriptManager* manager = [MDCycriptManager sharedInstance];
>           [manager loadCycript:NO];
>   
>           NSError* error;
>           NSString* result = [manager evaluateCycript:@"UIApp" error:&error];
>           NSLog(@"result: %@", result);
>           if(error.code != 0){
>               NSLog(@"error: %@", error.localizedDescription);
>           }
>   #endif
>           
>       }];
>   }
>   ```
>
>   

> * cy66: 进入cy的交互界面
>
>   ```
>   alias cy66='/Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/cycript_0.9.594/cycript -r 127.0.0.1:6666'
>   ```
>
>   

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost CYListenServer 开发集成cycript -t Cycript
> #原来""的参数，需要自己加上""
```

