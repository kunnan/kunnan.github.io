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

本文主要以MonkeyApp 为例，Xcode是V8.3.3 


# 创建MonkeyApp项目: 2018wxrobot

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

