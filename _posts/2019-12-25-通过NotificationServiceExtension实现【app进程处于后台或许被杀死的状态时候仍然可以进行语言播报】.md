---
layout: post
title: 通过NotificationServiceExtension实现【app进程处于后台或许被杀死的状态时候仍然可以进行语言播报】
date: 2019-12-25
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: -t
---





# 前言

- 想要消息推送的消息在Service Extension中被处理，需要aps 内容中包含`mutable-content : 1 `. (以极光平台推送为例，测试的时候，需要在高级设置开启mutable-content)

```objectivec
    aps =     {
alert =         {
    body = 11;
    subtitle = 111;
    title = "111223411.34";
};
badge = 1;
"mutable-content" = 1;
sound = default;
};
hasHandled = 1; //标记已经在Extension中被处理，防止重复的语言播报或者打印交易小票等冗余动作。
}

```



# 创建NotificationServiceExtension

- 选择新建Notification Service Extension 
- 实现UNNotificationServiceExtension



# NotificationService.m

```objectivec
@interface NotificationService ()

@property (nonatomic, strong) void (^contentHandler)(UNNotificationContent *contentToDeliver);
@property (nonatomic, strong) UNMutableNotificationContent *bestAttemptContent;

@end

@implementation NotificationService
- (void)didReceiveNotificationRequest:(UNNotificationRequest *)request withContentHandler:(void (^)(UNNotificationContent * _Nonnull))contentHandler {
    self.contentHandler = contentHandler;
    self.bestAttemptContent = [request.content mutableCopy];
    
    
    NSLog(@"NotificationService_%@: dict->%@", NSStringFromClass([self class]), self.bestAttemptContent.userInfo);
    
    self.bestAttemptContent.sound = nil;
#warning 如果系统大于12.0就走语音包合成文件播报方法
    if (yjIOS10) {
        __weak typeof(self) weakSelf = self;
        [[YJAudioTool sharedPlayer] playPushInfo:weakSelf.bestAttemptContent.userInfo backModes:YES completed:^(BOOL success) {
            __strong typeof(weakSelf) strongSelf = weakSelf;
            if (strongSelf) {
                
                NSMutableDictionary *dict = [strongSelf.bestAttemptContent.userInfo mutableCopy] ;
                    [dict setObject:[NSNumber numberWithBool:YES] forKey:@"hasHandled"] ;
                
                strongSelf.bestAttemptContent.userInfo = dict;
                
                
                strongSelf.contentHandler(self.bestAttemptContent);
            }
        }];
    } else {
        self.contentHandler(self.bestAttemptContent);
    }
}

- (void)serviceExtensionTimeWillExpire {
    // Called just before the extension will be terminated by the system.
    // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.
    self.contentHandler(self.bestAttemptContent);
}


```



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/mac/bin/knpost 通过NotificationServiceExtension实现【app进程处于后台或许被杀死的状态时候仍然可以进行语言播报】 -t iOS
> #原来""的参数，需要自己加上""
```

