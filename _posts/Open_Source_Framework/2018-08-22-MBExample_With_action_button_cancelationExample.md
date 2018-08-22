---
layout: post
title: MBExample_With_action_button_cancelationExample
date: 2018-08-22
tag: Open_Source_Framework
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 展示包含progress、hud.label、hud.detailsLabel的MBProgressHUD
---

# 前言



![image](https://ws4.sinaimg.cn/large/af39b376gy1fuij1famv5j205g047jsf.jpg)

# code



```objc
 BOOL _canceled;
static MBProgressHUD *hud ;
+ (void)doSomeWorkWithProgress {
    _canceled = NO;
    // This just increases the progress indicator in a loop.
    [self gethardwareInfo];// 获取设备信息
    
    float progress = 0.5f;
//    while (progress < 1.0f) {
//        if (_canceled) break;
//        progress += 0.01f;
//        dispatch_async(dispatch_get_main_queue(), ^{
//            // Instead we could have also passed a reference to the HUD
//            // to the HUD to myProgressTask as a method parameter.
            [MBProgressHUD HUDForView:[ASLogWindow sharedInstance]].progress = progress;
//        });
//        usleep(50000);
//    }
}
+  (void)cancelWork:(id)sender {
    NSLog(@"cancelWork");
    _canceled = YES;
    dispatch_async(dispatch_get_main_queue(), ^{
        [hud hideAnimated:YES];
    });
}
+ (void)cancelationExample {
    
    hud = [MBProgressHUD showHUDAddedTo:[ASLogWindow sharedInstance] animated:YES];
    // Set the determinate mode to show task progress.
    hud.mode = MBProgressHUDModeDeterminate;
    hud.label.text = NSLocalizedString(@"获取设备信息...\n ", @"HUD loading title");
    hud.detailsLabel.text = NSLocalizedString(@"如果长时间没获取到，请联系服务端人员进行排查\n(0/1)", @"HUD title");

    // Configure the button.
    [hud.button setTitle:NSLocalizedString(@"Cancel", @"HUD cancel button title") forState:UIControlStateNormal];
    [hud.button addTarget:self action:@selector(cancelWork:) forControlEvents:UIControlEventTouchUpInside];
    
    dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
        // Do something useful in the background and update the HUD periodically.
        [self doSomeWorkWithProgress];//        获取信息
        // 隐藏     //  如果收到设备信息才hidden loding
        
    });
}
```





# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost MBExample_With_action_button_cancelationExample 展示包含progress、hud.label、hud.detailsLabel的MBProgressHUD -t Open_Source_Framework
> #原来""的参数，需要自己加上""
```

