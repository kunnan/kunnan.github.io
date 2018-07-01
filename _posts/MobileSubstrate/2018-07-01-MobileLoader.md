---
layout: post
title: MobileLoader
date: 2018-07-01
tag: MobileSubstrate
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 将第三方动态库加载到运行的目标应用中
---



# MobileLoader

#### MobileLoader加载dylib的过程 

 首先通过环境变量`DYLD_INSERT_LIBRARIES`把它自己加载到目标应用里面，然后查找特定目录（具体的目录依赖于越狱工具的加载机制，通常在`/Library/MobileSubstrate/DynamicLibraries/ `;coolstar的Electra越狱的iOS 11  /bootstrap/Library/SBInject  对应之前的系统的路径DynamicLibraries）下的动态库plist文件，如果plist文件的配置信息符合当前运行的应用，就会通过`dlopen`函数打开对应的dylib文件。



> * `DYLD_INSERT_LIBRARIES`的使用例子
>
>   ```
>   int main() {
>     const char kEnvName[] = "DYLD_INSERT_LIBRARIES";
>     printf("%s=%s\n", kEnvName, getenv(kEnvName));
>     // CHECK: DYLD_INSERT_LIBRARIES=.*darwin-dummy-shared-lib-so.dylib.*
>     return 0;
>   }
>   ```
>
>   

> * `dlopen`的使用例子
>
>   ```
>   -(void)antiDebug{
>       // 反调试
>       void* handle = dlopen(0, RTLD_GLOBAL | RTLD_NOW);
>       ptrace_ptr_t ptrace_ptr = dlsym(handle, [[GeneralUtil convertHexStrToString:NIANGAO_PTRACE] UTF8String]);
>       ptrace_ptr(PT_DENY_ATTACH, 0, 0, 0);
>       dlclose(handle);
>   }
>   ```
>
>   > * [AntiAntiDebug.m: 反反调试的代码  ](https://github.com/kunnan/AntiAntiDebug/blob/master/tweak/antiantidebug/Tweak.xm)
>   > * [关于反调试&反反调试那些事](http://www.alonemonkey.com/2017/05/25/*antiantidebug*/)[ ](https://github.com/HarryHaoLee)
>   > * [AntiAntiDebug.py](https://github.com/zhangkn/iOSREBook/tree/master/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/AntiAntiDebug)



#### tweak 中plist的重要过滤条件

> * dylib   注入所有进程的方式

![image](https://ws4.sinaimg.cn/large/af39b376ly1fsu3q7z9cej20ak03rq4p.jpg)

> * > - Filter字段
>   >
>   >   ```
>   >   # 所有普通app 
>   >   { Filter = { Bundles = ( "com.apple.UIKit" ); }; }
>   >   ```
>   >
>   >   ```
>   >   <!--特殊的app-->
>   >   { Filter = { Bundles = ( "com.tencent.xin","com.apple.springboard","com.apple.Preferences"); }; }
>   >   ```
>   >
>   >   ```
>   >   <!--Executables、Bundles 的配合使用 -->
>   >   <key>Executables</key>
>   >   <array>
>   >     <string>assntd</string>
>   >   </array>
>   >   
>   >     <key>Bundles</key>
>   >   <array>
>   >     <string>com.apple.ios.SKitUIService</string>
>   >   </array>
>   >   ```
>   >
>   >   



#### tweak 中的control 文件

> * Depends: mobilesubstrate (>= 0.9.5000), firmware (>= 3.0) ,system-cmds 

# other 

我们逆向开发的时候，处理开发一些动态库放在`/Library/MobileSubstrate/DynamicLibraries/ `,还会经常开发一些tool、资源文件，通过放在`Package/Layout/System/Library/Frameworks/*`、`Package/Layout/usr/bin/`。



> * [iOS11的越狱源码：](https://github.com/coolstar/electra/blob/master/docs/getting-started.md )



#### [FLEX (Flipboard Explorer) is a set of in-app debugging and exploration tools for iOS development](https://github.com/Flipboard/FLEX#learning-from-other-apps)



> * [AFlexLoader](https://github.com/zhangkn/KNAFlexLoader) A dylib Loader for Flex, You can use it to analyse 3rd-party apps without sourcecode
>
> * [KNAFlexLoader:Tweak.xm](https://github.com/zhangkn/KNAFlexLoader/blob/master/Tweak.xm)
>
>   ```
>   #import "lib/AFlexLoader.h"
>   //1、%ctor方法是tweak 初始化的入口，当MobileSubstrate注入dylib后会调用此方法。libFlex类会监听UIApplicationDidBecomeActiveNotification，所以当被注入的App变为激活状态后，FLEX就会显示出来
>   // 2、如果不使用%ctor，你使用__attribute__((constructor)) static void entry()方法是作为入口也是可以
>   %ctor {
>   
>   	//NSDictionary *preferences = [NSDictionary dictionaryWithContentsOfFile:@"/var/mobile/Library/Preferences/me.abit.AFlexLoader.plist"];
>   	NSDictionary *preferences = [NSDictionary dictionaryWithContentsOfFile:@"/User/Library/Preferences/me.abit.AFlexLoader.plist"];
>   	NSString *bundleID = [[NSBundle mainBundle] bundleIdentifier];
>   	NSString *loaderEnabledKey = [NSString stringWithFormat:@"AFlexLoaderEnabled-%@", bundleID];
>   	if ([preferences[loaderEnabledKey] boolValue]) {
>   		[[NSNotificationCenter defaultCenter] addObserver:[AFlexLoader sharedInstance] selector:@selector(showExplorer) name:UIApplicationDidBecomeActiveNotification object:nil];
>   		NSLog(@"AFlexLoader: injected successfully");
>   	} else {
>   		NSLog(@"AFlexLoader: disabled");
>   	}
>   }
>   ```
>
>   > *[SpringBoard-with-FLEX for ios11](https://www.lanvsblue.top/2018/01/23/SpringBoard-with-FLEX/)

# See Also 

>* [NewsHooker](https://github.com/kunnan/NewsHooker)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost MobileLoader 将第三方动态库加载到运行的目标应用中 -t MobileSubstrate
> #原来""的参数，需要自己加上""
```

