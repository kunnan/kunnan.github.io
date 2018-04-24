---
layout:     post
title:      Xcode_Debug
subtitle:   iOS开发调试Bug的方法
date:       2016-10-18
author:     kunnan
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - 开发技巧
    - Debug
---


# 前言

Debug,除了解决bug,还常常是逆向分析的手段之一；

#### 个人习惯的分析方式:运行时API+LLDB +CycriptTricks+ powerful-private-methods

>* 我最喜欢的就是lldb结合私有方法[powerful-private-methods](https://kunnan.github.io/2018/04/20/CycriptTricks/)以及[CycriptTricks](https://zhangkn.github.io/2017/12/CycriptTricks/)进行分析。

>* [howToLocateTheBlockImplementation](https://zhangkn.github.io/2018/04/howToLocateTheBlockImplementation/)
>```
>memory read –size 4 –format x 进行代码定位
>```


# 基础概念

#### lldb 相关的概念

>* ASLR offset: 进程在虚拟内存相对于模块基地址的偏移量
>```
>image list -o -f |grep knPenFaceBeauty
>```

>* 模块在内存中的起始地址----模块基地址

>* [lldb命令详解](https://segmentfault.com/a/1190000012309948)
>* [Attaching with LLDB](https://segmentfault.com/a/1190000012175091)
>* [LLDB breakpoint syntax](https://segmentfault.com/a/1190000012134819)
>* [tweak 中常用的方法调用方法和 运行时API
](https://segmentfault.com/a/1190000011694026)

>* [Hooking & Executing Code with dlopen & dlsym -- C functions](https://segmentfault.com/a/1190000011673505)
>

# [断点调试](https://segmentfault.com/blog/reverseengineering)

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fqnr10db2qj30ck06mgm6.jpg)

- 普通断点
- 全局断点
- 条件断点

#### 1.普通断点 略


#### 2.全局断点 All Exceptions

当程序运行出现崩溃时，就会自动断点到出现crash的代码行

#### 3.条件断点

>* 编辑断点

>* 添加条件Condition

>* 还可以Action中在条件断点触发时执行事件
如：输出信息

#### [4.方法断点](https://segmentfault.com/a/1190000011679340)

>* [Stopping in Code](https://segmentfault.com/a/1190000011679340)
>


# 打印调试（NSLog）



#### 强化NSLog

```
//A better version of NSLog
#define NSLog(format, ...) do { \
fprintf(stderr, "<%s : %d> %s\n", \
[[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], \
__LINE__, __func__); \
(NSLog)((format), ##__VA_ARGS__); \
fprintf(stderr, "-------\n"); \
} while (0)
```
控制台输出

```
<ViewController.m : 32> -[ViewController viewDidLoad]
2016-10-14 17:33:31.022 DEUBG[12852:1238167] Hello World！
-------
```

#### 利用NSString输出多种类型

![](http://ww4.sinaimg.cn/large/65e4f1e6gw1f8rxvn6fqlj20nc05cgoh.jpg)

#### 开启僵尸对象 enable Zomble Objects

Xcode可以把那些已经release掉得对象，变成“僵尸”，当我们访问一个Zombie对象时，Xcode可以告诉我们正在访问的对象是一个不应该存在的对象了。因为Xcode知道这个对象是什么，所以可以让我们知道这个对象在哪里，以及这是什么时候发生的。

>* 具体这样做：(僵尸只能用在模拟器和OC语言) enable Zomble Objects
>```
>Product ->Scheme->run->Diagnostics-> enable Zomble Objects
>```

# [lldb](https://zhangkn.github.io/2018/04/howToLocateTheBlockImplementation/)


LLDB 是一个有着 REPL 的特性和 C++ ,Python 插件的开源调试器。


#### 基础

###### *help*
在控制台输入`help`，显示控制台支持的lldb命令

###### *print* 缩写`p` 

print是 `expression --` 的缩写

>* print 可以指定格式打印 `默认 p` `十六进制 p/x`、`二进制 p/t`
>
>```
(lldb) p 16
16
(lldb) p/x 16
0x10
(lldb) p/t 16
0b00000000000000000000000000010000
(lldb) p/t (char)16
0b00010000
```

你也可以使用 p/c 打印字符，或者 p/s 打印以空终止的字符串  p/d打印ACRSII(译者注：以 '\0' 结尾的字符串)。

>* [点击查看更多清单](https://sourceware.org/gdb/onlinedocs/gdb/Output-Formats.html)


###### *po*

打印对象，是 `e -o --`的缩写



#### 流程控制：continue，step over，step into，step out




>* continue 
>```
>在 LLDB 中，你可以使用 process continue 命令来达到同样的效果，它的别名为 continue，或者也可以缩写为 c。
```

>* step over
>```
>LLDB 则可以使用 thread step-over，next，或者 n 命令。
```

>* step in
>```
>LLDB中使用 thread step in，step，或者 s 命令。注意，当前行不是函数调用时，next 和 step 效果是一样的。
```
>* step out
>
>
>
###### frame info

会告诉你当前的行数和源码文件

```
(lldb) frame info
frame #0: 0x000000010a53bcd4 DebuggerDance`main + 68 at main.m:17

```

###### Thread Return 


`(lldb) thread return NO`



# instruments



**leaks**内存泄漏检查工具

![](http://ww4.sinaimg.cn/large/006y8lVagw1f8ve5wnnr6j30li0c1wgd.jpg)

运行后查看

![](http://ww4.sinaimg.cn/large/006y8lVagw1f8vebiu6r5j30se0kdqcr.jpg)

# [View视图调试-Reveal的替代产品](https://zhangkn.github.io/2017/12/KNAFlexLoader/)

>* Debug View Hierarchy
>```
>从菜单中选择Debug > View Debugging > Capture View Hierarchy
```
>* [我更喜欢的View调试方式KNAFlexLoader](https://zhangkn.github.io/2017/12/KNAFlexLoader/)
>
>* cycript 的调试方式也不错

# 模拟器调试

编译并运行应用程序，选中模拟器，从 Debug菜单中选择Color Blended Layers选项。



# see also

>* [参考博文](http://www.cnblogs.com/daiweilai/p/4421340.html#quanjuduandian)
>* [《The LLDB Debugger》](http://lldb.llvm.org/tutorial.html)
>* [《About LLDB and Xcode》](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/Introduction.html)
>* [《LLDB调试命令初探》](http://www.starfelix.com/blog/2014/03/17/lldbdiao-shi-ming-ling-chu-tan/)
>* [《与调试器共舞 - LLDB 的华尔兹》](http://objccn.io/issue-19-2/)
>* [otool](https://zhangkn.github.io/2017/12/otool/)
>* [The LLDB Debugger](https://lldb.llvm.org/lldb-gdb.html)
>* [KNAFlexLoader](https://zhangkn.github.io/2017/12/KNAFlexLoader/)
>* [assemblyLanguage](https://zhangkn.github.io/2017/12/assemblyLanguage/)
>* [CycriptTricks](https://zhangkn.github.io/2017/12/CycriptTricks/)
>* [howToLocateTheBlockImplementation](https://zhangkn.github.io/2018/04/howToLocateTheBlockImplementation/)
>```
>hopper + lldb + knhook + cycript + powerful-private-methods 
>```
>* [可以class-dump混编的](https://github.com/zhangkn/KNBin/blob/master/swiftOCclass-dump)
>
>* [获取头文件，并简单的猜测使用了哪些第三方库CocoaPods](https://github.com/zhangkn/KNBin/blob/master/knhead)
>* [_shortMethodDescription 用于LLDB 进行打断点](https://segmentfault.com/a/1190000012196362)
>* [Hopper + LLDB](https://blog.csdn.net/z929118967/article/details/78264816)
- [ 配置debugserver](https://blog.csdn.net/z929118967/article/details/78262460)
- [usbmuxd ](http://blog.csdn.net/z929118967/article/details/78201784)
- [Anti ptrace:去掉AlipayWallet的ptrace 反调试保护，进行lldb调试---仅用于参考学习](https://segmentfault.com/a/1190000012199291)
- [ iOS逆向工程之Hopper+LLDB调试第三方App](https://blog.csdn.net/z929118967/article/details/78263055)
- [一步一步用debugserver + lldb代替gdb进行动态调试](http://iosre.com/t/debugserver-lldb-gdb/65)
- [ 配置debugserver](https://blog.csdn.net/z929118967/article/details/78262460)
- [how-to-locate-the-block](http://iosre.com/t/when-you-come-across---nsxxxblock---0xrandomnumber-while-debugging-a-block-in-lldb-how-to-locate-the-block/4977)
- [powerful-private-methods](http://iosre.com/t/powerful-private-methods-for-debugging-in-cycript-lldb/3414)

