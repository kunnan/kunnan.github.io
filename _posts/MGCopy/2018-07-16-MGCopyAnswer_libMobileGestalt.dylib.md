---
layout: post
title: MGCopyAnswer_libMobileGestalt.dylib
date: 2018-07-16
tag: MGCopy
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 硬件信息的获取和修改
---



# 前言

> * ## MGCopyAnswer 是**libMobileGestalt** 中的一个函数



#### libMobileGestalt.dylib

**libMobileGestalt** is a library that can be used to get various system values such as the UDID, disk usage, device version and much more. It is comparable to [liblockdown.dylib](http://iphonedevwiki.net/index.php/Liblockdown.dylib). See also [lockdownd](http://iphonedevwiki.net/index.php/Lockdownd). 

#  获取设备信息

> * 声明
>
>   ```
>   // extern CFStringRef MGCopyAnswer(CFStringRef key) WEAK_IMPORT_ATTRIBUTE;
>   ```
>
>   > *  执行
>   >
>   >   ```
>   >   %ctor {
>   >   	NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
>   >   	%init();
>   >   	[[NSNotificationCenter defaultCenter] addObserverForName:UIApplicationDidFinishLaunchingNotification object:nil queue:[NSOperationQueue mainQueue] usingBlock:^(NSNotification *block) {
>   >   		CFStringRef uniqueIdentifier = MGCopyAnswer(CFSTR("UniqueDeviceID"));
>   >   		UIAlertView *av = [[UIAlertView alloc] initWithTitle:@"UDID" message:(NSString *)uniqueIdentifier delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil];
>   >   		if (uniqueIdentifier)
>   >   			CFRelease(uniqueIdentifier);
>   >   		[av show];
>   >   		[av release];
>   >   	}];
>   >   	[pool drain];
>   >   }
>   >   
>   >   ```
>   >
>   >   

> * [**UDIDAlerter**](https://github.com/rpetrich/UDIDAlerter/blob/6f6741add33962823d98d5637d44f275fe2a9a8e/Tweak.x)
>
>   ```
>   Filter = {Bundles = ("com.apple.UIKit");Executables = ("MobileGestaltHelper");Mode = Any;};
>   ```
>
>   

# 例子

#### [使用capstone进行MGCopyAnswer方法地址获取，使用MSHookFunction 进行hook的例子](https://github.com/kunnan/MobileGestaltHooking)

One of the most abused API is `MGCopyAnswer` in libMobileGestalt, but directly hooking it will instantly crash the process with an `invalid instruction`. 

Fortunately, we have [Capstone Engine](http://www.capstone-engine.org/), which is a powerful disassembler based on LLVM’s MC to save the day.

> * [HookingMGCopyAnswerLikeABoss](https://mayuyu.io/2017/06/26/HookingMGCopyAnswerLikeABoss/)



# See Also 

>* [**libMobileGestalt.dylib** **strings**](https://github.com/keith/Xcode.app-strings/blob/e8ae4a1bcd21100e5faf30abd8c218af2c6a3b84/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/usr/lib/libMobileGestalt.dylib)
>
>  ```
>  Xcode.app-strings/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Library/CoreSimulator/Profiles/Runtimes/iOS.simruntime/Contents/Resources/RuntimeRoot/usr/lib/libMobileGestalt.dylib
>  ```
>
>  
>
>* [**Capstone** is a lightweight multi-platform, multi-architecture disassembly framework.](http://www.capstone-engine.org/)
>
>* [**libMobileGestalt**](http://iphonedevwiki.net/index.php/LibMobileGestalt.dylib)
>
>* [mgcopy 的key](https://github.com/zhangkn/ios_ssl_proxy/blob/master/docs/mgcopy.md)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost MGCopyAnswer_libMobileGestalt.dylib 硬件信息的获取和修改 -t MGCopy
> #原来""的参数，需要自己加上""
```

