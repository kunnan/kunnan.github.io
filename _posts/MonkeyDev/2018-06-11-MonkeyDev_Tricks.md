---
layout: post
title: MonkeyDev_Tricks
date: 2018-06-11
tag: MonkeyDev
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: MonkeyDev 的使用小技巧
---

# 前言



#### 依赖环境

> * theos
> * ldid
> * sshpass,或者设置免密码登录

本文主要以MonkeyApp 为例，Xcode是V8.3.3 ;

> * 动态库其实是存放在目标app的Frameworks目录
>
>   ```
>   /Users/devzkn/Library/Developer/Xcode/DerivedData/weixin-fqltqjyxecppixbrhoqzzbtyrgmz/Build/Products/Debug-iphoneos/weixin.app/Frameworks/libweixinDylib.dylib
>   ```
>
>   

# 0、创建logos tweak



logostweak 依赖cydiasubsrate库，项目会自动链接。

> * command +B ,以debug模式安装
> * command+ shift+ i，将以release模式安装



# I、captainHook tweak

直接通过导入captionHook.h（利用了oc 的runtime特性） 文件，利用里面的宏进行hook。

> * 它不需要语法转换，也不依赖cydiaSubstrate动态库。
>
> * 原理分析： 对特定文件进行预处理，当然也可以转换为汇编代码。
>
>   ![image](https://wx1.sinaimg.cn/large/af39b376gy1ft29irxda2j20dq0cctbk.jpg)
>
>
> * 例子
>
>   ```
>   #import <Foundation/Foundation.h>
>   #import "CaptainHook/CaptainHook.h"
>   @interface ViewController : NSObject
>   @property (nonatomic, retain) NSString* newProperty;
>   - (void)addMethod:(NSString*) output;
>   @end
>   CHDeclareClass(ViewController)// 声明一个类
>   CHPropertyRetainNonatomic(ViewController, NSString*, newProperty, setNewProperty);//增加属性；当然使用AssociatedObject 也是很简单
>   CHDeclareMethod1(void, ViewController, addMethod, NSString*, output){// 增加方法
>       NSLog(@"add method %@", output);
>   }
>   CHOptimizedMethod2(self, void, ViewController, instanceMethodUsername, NSString*, username, password, NSString*, password){//hook 目标函数
>       NSString* ppassword = CHIvar(self,_password,__strong NSString*);// 获取私有属性，当然我更信息KVC的方式；[#0x183d6e00 valueForKey:@"m_delegate"]
>       CHDebugLog(@"private password: %@", ppassword);    
>       ppassword = [self valueForKey:@"_password"];    
>       CHDebugLog(@"kvc password: %@", ppassword);    
>       self.newProperty = @"set new property";    
>       CHDebugLog(@"new property is:%@", self.newProperty);    
>       [self addMethod:@"output"];    
>       CHDebugLog(@"hook instance method username: %@, password: %@", username, password);    
>       CHSuper2(ViewController, instanceMethodUsername, username, password, password);//调用原函数实现
>   }
>   CHOptimizedClassMethod2(self, void, ViewController, classMethodUsername, NSString*, username, password, NSString*, password){//hook类方法
>       CHDebugLog(@"hook class method username: %@, password: %@", username, password);
>       CHSuper2(ViewController, classMethodUsername, username, password, password);
>   }
>   CHConstructor{//注册hook，加载类
>       CHLoadLateClass(ViewController);//加载类
>       CHHook2(ViewController, instanceMethodUsername, password);//注册hook
>       CHHook2(ViewController, classMethodUsername, password);//注册hook    
>       CHHook0(ViewController, newProperty);//注册hook
>       CHHook1(ViewController, setNewProperty);//注册hook
>   }
>   ```
>
>   > * 扩展知识
>   >
>   >   *  彩英associatedObject增加属性
>   >
>   >     ```
>   >     //结合@dynamic的 associatedObject例子
>   >     @implementation NSObject (AssociatedObject)
>   >     @dynamic associatedObject;
>   >     - (void)setAssociatedObject:(id)object {
>   >         objc_setAssociatedObject(self,
>   >     @selector(associatedObject), object,
>   >     OBJC_ASSOCIATION_RETAIN_NONATOMIC);
>   >     }
>   >     - (id)associatedObject {
>   >         return objc_getAssociatedObject(self,
>   >     @selector(associatedObject));
>   >     }
>   >     ```
>   >
>   >     



# II 、cli : command-line tool

[命令行工具，常用来新增一个守护进程的工具类](https://zhangkn.github.io/2017/12/iphoneDaemonTool/)









# # III、创建MonkeyApp项目: 2018wxrobot

>* 项目结构

```
devzkndeMacBook-Pro:2018wxrobot devzkn$ tree -L 2
.
├── 2018wxrobot
│   ├── Config
│   ├── Info.plist
│   ├── Scripts
│   ├── Target.plist -> /Users/devzkn/code/tweak/knwx2018/2018wxrobot/2018wxrobot/TargetApp/WeChat.app/Info.plist
│   ├── TargetApp
│   ├── WeChat_Headers
│   ├── icon.png
│   └── tmp
├── 2018wxrobot.xcodeproj
│   ├── project.pbxproj
│   ├── project.xcworkspace
│   └── xcuserdata
├── 2018wxrobotDylib
│   ├── 2018wxrobotDylib-Prefix.pch
│   ├── AntiAntiDebug
│   ├── Config
│   ├── Logos
│   ├── Tools
│   ├── _018wxrobotDylib.h
│   ├── _018wxrobotDylib.m
│   └── fishhook
└── LatestBuild -> /Users/devzkn/Library/Developer/Xcode/DerivedData/2018wxrobot-hdmuykldweghmdfknuauszhntoyn/Build/Products/Debug-iphoneos
```

#### Target.plist 


```
devzkndeMacBook-Pro:2018wxrobot devzkn$ rm -rf Target.plist
devzkndeMacBook-Pro:2018wxrobot devzkn$ ln -s /Users/devzkn/decrypted/frida-ios-dump-master/Payload/WeChat.app/Info.plist Target.plist
```

运行之后，右重新替换为

```
Target.plist -> /Users/devzkn/code/tweak/knwx2018/2018wxrobot/2018wxrobot/TargetApp/WeChat.app/Info.plist
```



#### 方法跟踪日志

 

###### 开启日志跟踪，直接配置MDConfig.plist

- ENABLE: 设置为YES开启方法跟踪，默认为NO。
- 在`Build Settings`里面设置`MONKEYDEV_RESTORE_SYMBOL`为`YES`，方便后面设置符号断点

 





# build

> * 编译的时候，记得选择通用设备。

![image](https://ws1.sinaimg.cn/large/af39b376gy1fsxypct1gij208t01cjrb.jpg)

# Cycript

#### `cy66` 

>*      这里Cycript默认端口是6666  ` CYListenServer(6666);`

```sh
 /Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/cycript_0.9.594/cycript -r 192.168.2.14:6666
 [[[UIWindow keyWindow] rootViewController] _printHierarchy].toString()
```

>* 端口转发6666

```
devzkndeMacBook-Pro:cycript_0.9.594 devzkn$ port6666
Forwarding local port 6666 to remote port 6666
```

```
 /Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/cycript_0.9.594/cycript -r 127.0.0.1:6666
```

>* alias

```
alias cy6666='/Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/cycript_0.9.594/cycript -r 127.0.0.1:6666'
```
```
knbash
```

```
cy66
```

# Q&A

>* `Enable Strict Checking of objc_msgSend Calls` 设置为NO，否则`objc_msgSend `无法使用

```
"Enable Strict Checking of objc_msgSend Calls" is a compile time check, not run time, so there is no benefit to turning it off in production.
```

>* Q: 10.13系统下用cycript报错:

```
dyld: Library not loaded: /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/libruby.2.0.0.dylib
Referenced from: /Users/Jveryl/Desktop/Crash/cycript_0/Cycript.lib/cycript-apl
Reason: image not found
Abort trap: 6
```

>* A: 先关闭系统的SIP，然后运行如下命令，把原来引用的位置创建符号链接到现在新版本的位置:


```sh
sudo mkdir -p /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/
sudo ln -s /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/libruby.2.3.0.dylib /System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/lib/libruby.2.0.0.dylib
```

or 

```
brew install ruby@2.0


devzkndeMacBook-Pro:Versions devzkn$  cp -a  /usr/local/Cellar/ruby@2.0/2.0.0-p648_6/lib/libruby.2.0.0.dylib /Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/cycript_0.9.594/Cycript.lib
```


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost MonkeyDev_Tricks MonkeyDev 的使用小技巧 -t MonkeyDev
> #原来""的参数，需要自己加上""
```

