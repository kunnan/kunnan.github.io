---
layout: post
title: UIDeviceBattery
date: 2018-09-06
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 电池的状态处理：电池状态获取及监测、电池电量获取及监测、低电量模式切换监测
---



# 前言

电量低于 <= 50 %进行报警

> * name:NSProcessInfoPowerStateDidChangeNotification
>
> * UIDeviceBatteryLevelDidChangeNotification
>
>   ```
>       [[NSNotificationCenter defaultCenter]
>        addObserverForName:UIDeviceBatteryLevelDidChangeNotification
>        object:nil queue:[NSOperationQueue mainQueue]
>        usingBlock:^(NSNotification *notification) {
>            // Level has changed
>            [UIDevice currentDevice].batteryMonitoringEnabled = YES;
>            NSLog(@"Battery Level Change");
>            NSLog(@"电池电量：%.2f", [UIDevice currentDevice].batteryLevel);
>        }];
>   
>   ```
>
> * UIDeviceBatteryStateDidChangeNotification

# 一、电池状态获取及监测



> * code
>
>   ```
>   #pragma mark - 电池状态获取及监控
>   
>   -(void)checkAndMonitorBatteryState{
>   
>   
>   
>   UIDevice * device = [UIDevice currentDevice];
>   
>   //是否允许监测电池
>   
>   //要想获取电池状态和监控电池状态 必须允许
>   
>   device.batteryMonitoringEnabled = true;
>   
>   //1、check
>   
>   /*
>   
>   电池状态
>   
>   typedef NS_ENUM(NSInteger, UIDeviceBatteryState) {
>   
>   UIDeviceBatteryStateUnknown,
>   
>   UIDeviceBatteryStateUnplugged, // on battery, discharging
>   
>   UIDeviceBatteryStateCharging, // plugged in, less than 100%
>   
>   UIDeviceBatteryStateFull, // plugged in, at 100%
>   
>   } __TVOS_PROHIBITED;
>   
>   */
>   
>   UIDeviceBatteryState state = device.batteryState;
>   
>   NSArray *stateArray = [NSArray arrayWithObjects:@"未开启监视电池状态",@"电池未充电状态",@"电池充电状态",@"电池充电完成",nil];
>   
>   NSLog(@"电池状态：%@", [stateArray objectAtIndex:state]);
>   
>   
>   
>   //2、monitor
>   
>   [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didChangBatteryState:) name:@"UIDeviceBatteryStateDidChangeNotification" object:device];
>   
>   }
>   
>   
>   
>   -(void)didChangBatteryState:(NSNotification *)notification{
>   
>   //电池状态发生改变时调用
>   
>   
>   
>   }
>   
>   
>   
>   ```
>

# 二、电池电量获取及监测

> * UIDeviceBatteryLevelDidChangeNotification
>
>   ```
>   #pragma mark - 电池电量获取及监控
>   
>   -(void)checkAndMonitorBatteryLevel{
>   
>   
>   
>   //拿到当前设备
>   
>   UIDevice * device = [UIDevice currentDevice];
>   
>   
>   
>   //是否允许监测电池
>   
>   //要想获取电池电量信息和监控电池电量 必须允许
>   
>   device.batteryMonitoringEnabled = true;
>   
>   
>   
>   //1、check
>   
>   /*
>   
>   获取电池电量
>   
>   0 .. 1.0. -1.0 if UIDeviceBatteryStateUnknown
>   
>   */
>   
>   float level = device.batteryLevel;
>   
>   NSLog(@"level = %lf",level);
>   
>   
>   
>   //2、monitor
>   
>   [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(didChangeBatteryLevel:) name:@"UIDeviceBatteryLevelDidChangeNotification" object:device];
>   
>   
>   
>   }
>   
>   
>   
>   - (void)didChangeBatteryLevel:(id)sender{
>   
>   //电池电量发生改变时调用
>   
>   UIDevice *myDevice = [UIDevice currentDevice];
>   
>   [myDevice setBatteryMonitoringEnabled:YES];
>   
>   float batteryLevel = [myDevice batteryLevel];
>   
>   NSLog(@"电池剩余比例：%@", [NSString stringWithFormat:@"%f",batteryLevel*100]);
>   
>   }
>   
>   
>   ```
>

# 三、低电量模式切换监测



> * isLowPowerModeEnabled
>
>   ```
>   #pragma mark - 低电量模式切换
>   
>   -(void)checkAndMonitorPowerMode{
>   
>   //1、check
>   
>   //是否处于低电量模式
>   
>   if ([[NSProcessInfo processInfo] isLowPowerModeEnabled]) {
>   
>   NSLog(@"处在低电量模式");
>   
>   }
>   
>   else{
>   
>   NSLog(@"未处于低电量模式");
>   
>   }
>   
>   
>   
>   //2、monitor
>   
>   //低电量模式切换通知
>   
>   [[NSNotificationCenter defaultCenter] addObserver:self
>   
>   selector:@selector(didChangePowerMode:)
>   
>   name:NSProcessInfoPowerStateDidChangeNotification
>   
>   object:nil];
>   
>   
>   
>   }
>   
>   
>   
>   //收到低电量通知之后调用的方法
>   
>   //PS:手动设置低电量模式时，程序会回到后台，当程序从后台回到前台时就会调用该方法
>   
>   - (void)didChangePowerMode:(NSNotification *)notification {
>   
>   if ([[NSProcessInfo processInfo] isLowPowerModeEnabled]) {
>   
>   NSLog(@"打开低电量模式");
>   
>   } else {
>   
>   NSLog(@"关闭低电量模式");
>   
>   }
>   
>   
>   ```
>

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost UIDeviceBattery 电池的状态处理：电池状态获取及监测、电池电量获取及监测、低电量模式切换监测 -t objc
> #原来""的参数，需要自己加上""
```

