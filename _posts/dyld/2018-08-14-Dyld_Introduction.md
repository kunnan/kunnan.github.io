---
layout: post
title: Dyld_Introduction
date: 2018-08-14
tag: dyld
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 动态库： 静态库、动态库的区别，编译和注入；导出和隐藏符号表；
---





# 静态库和动态库的区别

![image](https://wx3.sinaimg.cn/large/af39b376gy1fu997tvkbhj20ca0egmyr.jpg)

* 动态库的特点

  * 存在形式有 .dylib，.framework 和链接符号 .tdb;
  * 它的好处是可以只保留一份文件和内存空间，从而能够被多个进程使用，例如系统动态库；
  * 可减小可执行文件的体积，不需要链接到目标文件。

   

   

   

* 静态库的特点

  * 以.a 或者.framework形式存在的一种共享程序代码的方式，从本质上来讲就是一种可执行文件的二进制形式；常常会将程序的部分功能编译成库，暴露出头文件的形式供开发者调用
  * 静态库以一个或者多个object文件组成；可以将一个静态库拆解成多个object文件（ar -x）
  * 静态库链接的时会直接链接到目标文件，并作为它的一部分存在。

# I 、[动态库的编译和注入](https://github.com/AloneMonkey/iOSREBook/tree/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93)



> * 动态库注入放入几种方式
>
>   * 通过增加load command 的`LC_LOAD_DYLIB`或者`LC_LOAD_WEAK_DYLIB`,指定动态库的路径来实现注入
>
>     * optool
>     * insert_dylib
>
>   * 通过`cydia substrate`提高的注入，配置plist文件，并将对应的plist、dylib文件放入指定目录（ /Layout/Library/MobileSubstrate/DynamicLibraries/、/usr/lib/TweakInject）；其实也是通过`DYLD_INSERT_LIBRARIES`将自己注入，然后遍历`DynamicLibraries`目录下的plist文件，再将符合规则的动态库通过`dlopen`打开
>
>   * 通过设置环境变量`DYLD_INSERT_LIBRARIES`指定要注入的动态库path
>
>     * 利用环境变量 DYLD_INSERT_LIBRARY 来添加动态库dumpdecrypted.dylib
>
>       ```
>       DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Containers/Bundle/Application/01ECB9D1-858D-4BC6-90CE-922942460859/KNWeChat.app/KNWeChat
>       
>       ```
>
>       

#    导出和隐藏符号



> * ` nm  -gm tmp_64.dylib ` 查看导出符号信息
>
>   ```
>   (__DATA,__data) external
>   (undefined) external _CFDataCreate (from CoreFoundation)
>                    (undefined) external _CFNotificationCenterGetDarwinNotifyCenter (from CoreFoundation)
>    (__TEXT,__text) external 
>                     (undefined) external _IOObjectRelease (from IOKit)
>                    (undefined) external _IORegistryEntryCreateCFProperty (from IOKit)
>   000000010ffa3f97 (__DATA,__objc_data) external _OBJC_CLASS_$_BslyjNwZmPCJkVst
>   000000010ffa3f97 (__DATA,__objc_data) external _OBJC_CLASS_$_ChiDDQmRSQpwQJgm
>   
>   ```
>
>   
>
> * 控制符号是否导出
>
>   * static 参数修饰,不会导出符号信息
>
>     ```
>     static char _person_name[30] = {'\0'};
>     
>     ```
>
>     
>
>   * [在编译参数中加入-exported_symbols_list export_list](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/ExportSymbol/Makefile)
>
>   * [在编译参数中指定-fvisibility=hidden，对指定符号增加visibility("default")来导出符号](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-6/6.5-%E5%8A%A8%E6%80%81%E5%BA%93/OC%20dylib/Makefile)
>
>     ```
>     //#define EXPORT __attribute__((visibility("default")))
>     
>     ```
>
>     





# See Also 

>* [dumpdecrypted](https://zhangkn.github.io/2017/12/dumpdecrypted/)
>
>* [iOSMixProject:马甲包混淆工程](https://github.com/kunnan/iOSMixProject)
>

>  * [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Dyld_Introduction 动态库： 静态库、动态库的区别，编译和注入；导出和银川符号表； -t dyld
> #原来""的参数，需要自己加上""
```

