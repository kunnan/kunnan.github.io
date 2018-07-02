---
layout: post
title: iosre_anti
date: 2018-07-01
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: iOS 应用逆向与安全
---



### 前言

 

> * `软件的逆向工程`指的是通过分析一个程序或系统的功能、结构或行为，将它的技术实现或设计细节推导出来的过程。
>
>   ```
>    当我们因为工作需要，或是对一个软件的功能很感兴趣，却又拿不到它的源代码时，往往可以通过逆向工程的方式对它进行分析，探索它的实现原理。 一个生动的比喻：照着配方包饺子,是正向开发 吃着饺子推配方（API 调用顺序）,是逆向工程。
>   ```
>
>   

# I、iOS 逆向分析方法

> * 从二进制文件入手，尝试从汇编代码中理清原始逻辑，并修改/绕过以达到目的。

>  

#### 1.0 通用的分析手段

**逆向流程：** 逆向的整个流程基本是相同的，而这些流程可以总结为：

- 获取应用的 ipa 包，解密（如果是越狱应用则不需要解密），导出头文件；

- 使用工具（cycript、[KNAFlexLoader:Tweak.xm,（mac 同系列工具： Reveal)](https://github.com/zhangkn/KNAFlexLoader/blob/master/Tweak.xm)、[knhook](https://github.com/zhangkn/hookClass/blob/master/hookClass/KNHookClass/KNHook.h)）通过界面查看 APP 的布局，从布局中找到对应的头文件，查看关键函数(使用[CycriptUsefulCommand](http://127.0.0.1:4000/2018/06/23/CycriptUsefulCommand/)以及[UIButton_sendActionsForControlEvents](https://kunnan.github.io/2018/06/08/UIButton_sendActionsForControlEvents/))；

- 使用`tweak`[hook](http://127.0.0.1:4000/2018/06/06/hookClass_knhook_hookClassLog/)、执行相关函数的输入输出以及调用栈，分析验证关键函数；

- 静态分析加动态调试进一步分析关键函数的实现逻辑

  ```
  1、使用hopper、ide分析代码逻辑，使用`lipo -info`   查看二进制文件的架构信息
  2、使用`lipo xx -thin arm64 -output xx_arm64` 进行瘦身
  ```

  

- 模拟或篡改原有 APP 的逻辑；

- 制作tweak或者移植到非越狱设备。

#### 1.1 静态分析

> * 在app没运行的情况下，借助反汇编工具（hopper\otool等）查看代码内部结构、语法、过程、接口，以及二进制文件结构信息、class-dump目标对象的class 信息等分析过程。 关键的地方做到准确提取信息：包括网络协议、数据格式、代码逻辑、控制流、表达式、接口和数据流。 
>
>   ```
>   “目标软件的二进制代码到汇编代码的翻译过程，我们称之为“反汇编
>   ```
>
>   

#### 1.2 网络分析

> * 抓包工具有 tcpdump, WireShark, Charles
>
>   ```
>   1、Wireshark可以详细的看到网络请求的三次握手，并且可支持spdy、tcp、udp、http等等的网络协议抓包；原理：将网卡设置为混杂模式；正常情况下，网卡会过滤目标地址不是自己的数据包；将网卡设置为混杂模式，就会收到经过网卡的所有数据包
>   2、charlesV4.2 的辅助功能：调试我们的接口(Compose、Repeat、breakpoints：临时修改、map:将本地请求重定向到一个文件或者将远程请求重定向到另一个请求、rewrite：对网络请求进行正则比配修改);支持http、https网络包的截取；原理是代理抓包。
>   ```
>
>   

#### 1.3  动态分析(动态调试、代码跟踪)

> * 分析者可以在程序的执行过程中观察程序的执行流程和计算结果。
>
>   ```
>   通常我通过借助lldb、Cycript、frida、AFLEXLoader、运行时常用的API等工具进行调试、分析代码，获取内存状态甚至修改内存，该过程包括反反调试、查看寄存器内容、跟踪函数执行结果等
>   ```
>
>   

#### 1.4 整体套路

 

以下步骤没有绝对的顺序

 

- 先dumpdecrypted（砸壳）、class-dump（导出OC头文件）寻找分析切入点；砸壳步骤可以使用[frida-ios-dump-master](https://zhangkn.github.io/2017/12/dumpdecrypted/#gsc.tab=0) 一次性完成。
- 用cycript、Frida、AFLEXLoader、[hookClass](https://github.com/zhangkn/hookClass/tree/master/hookClass/KNHookClass) 定位目标视图，获取目标的VC、delegate、关键函数
- 使用IDA、hopper、lldb 配合使用分析代码调用逻辑
- 编写tweak（借助theos、MonkeyDev进行编写）



# II、安全

- 使用二进制协议传输数据
- 类名方法名混淆、越狱检测、核心代码使用swift、c\c++ 实现
- 反调试



#### 2.0 把函数名隐藏在结构体里,以函数指针成员的形式存储

> * KNUtil.h
>
>   ```
>   @interface KNUtil : NSObject
>   /**
>    把函数名隐藏在结构体里，以函数指针成员的形式存储。
>    编译后，只留了下地址，去掉了名字和参数表，提高了逆向成本和攻击门槛.
>    */
>   typedef struct _util {
>       void (*cign)(char *kns[],unsigned int kncount, const char *knkey, unsigned char *knput);
>   }CNtKNil_t ;
>   #define SharedUtilStruct ([KNUtil sharedUtil])//提供给外围的接口
>   + (CNtKNil_t *)sharedUtil;
>   ```
>
>   > * KNUtil.m
>   >
>   >   ```
>   >   #include <stdio.h>
>   >   #include <string.h>
>   >   #include <stdlib.h>
>   >   //留给读者自己实践
>   >   ```
>   >
>   >   *  外围调用
>   >
>   >     ```
>   >     SharedUtilStruct->cign(key ,count,knkey, knput);
>   >     ```





#### See Also

>* [antiDebugger](https://zhangkn.github.io/2017/12/antiDebugger/)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost iosre_anti iOS 应用逆向与安全 -t iosre
> #原来""的参数，需要自己加上""
```

