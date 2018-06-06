---
layout: post
title: hookClass_knhook_hookClassLog
date: 2018-06-06
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: hookClassLog 打印类的执行方法
---


# [knhook](https://github.com/zhangkn/hookClass/blob/master/hookClass/KNHookClass/KNHook.h)



#### I、 `tweak+ knhook ` 进行跟踪 

>* 创建 tweak 

```sh
nic 
11 
MobileSubstrate Bundle filter: 可以使用frida-ps -Ua 进行查看
 List of applications to terminate upon installation: 使用 ps -e |grep No
```

>* 修改`Makefile`

```
THEOS_DEVICE_IP=usb2222	#5C9
TARGET = iphone:latest:8.0
ARCHS = armv7 arm64
THEOS=/opt/theos
THEOS_MAKE_PATH=$(THEOS)/makefiles
$(TWEAK_NAME)_CFLAGS += -Wno-error
```
>* 新增 deploy sh

```
echo "" > ~/.ssh/known_hosts
	cd `dirname $0` 
	make clean
	rm -rf ./packages
	make package install
```

#### II、 添加`knhook`

>* makefile

```makefile
KNHookClass/KNHook.m
```

>* xm 中引入KNHook.h

```xm
#import  "KNHookClass/KNHook.h"
```
#### III、 在UIApplicationDelegate 或者 `__attribute__((constructor)) static void before1(){
` 中进行执行

>* AppDelegate

```
//寻找UIApplicationDelegate 实例的didFinishLaunchingWithOptions
%hook _AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
 	%log;
	// 打印某个类的所有方法的，查看所有方法的执行顺序
		[KNHook hookClass:@""];// 具体的要结合hooper、cycript 进行定位, 
    return %orig;
}
%end
```
>* constructor

```objc
__attribute__((constructor)) static void before1(){

	[KNHook hookClass:@"WebViewController"];//WebViewJavascriptBridge
	}
```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost hookClass_knhook_hookClassLog hookClassLog 打印类的执行方法 -t iosre
> #原来""的参数，需要自己加上""
```
