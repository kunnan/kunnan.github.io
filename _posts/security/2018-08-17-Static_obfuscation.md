---
layout: post
title: Static_obfuscation
date: 2018-08-17
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 静态混淆，使用宏进行替换字符串，或者解析mach-o中对应的section进行类名和方法名的替换 
---



# 前言

本人最终喜欢的马甲包混淆方案是用[`Hikari`](https://github.com/HikariObfuscator/Hikari),具体的用法看[这里](https://kunnan.github.io/2018/06/05/iosObfuscation/)

> * 本文的重点是讲解最原始的方法：字符串替换
>   * 宏定义,[这里有更详细的讲解iOSobfuscation](https://zhangkn.github.io/2018/04/iOSobfuscation/)
>     * [**ios-class-guard**:Simple Objective-C obfuscator for Mach-O executables
>       * 此项目是class-dump的fork，过滤系统库符号，生成需要混淆的符号
>   * 修改二进制文件对应的section



# [ 宏定义的例子请看这](https://github.com/AloneMonkey/iOSREBook/tree/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.2%20%E9%9D%99%E6%80%81%E6%B7%B7%E6%B7%86/TargetApp/TargetApp)



#  二进制文件修改

从`__TEXT,objc_classname`和`__TEXT,objc_methname` 中读取对应的类名和方法名。


# 静态分析防护  小结

> * 1.对抗hopper和ida的分析可以修改macho文件的某些偏移量, 让hopper和ida无法分析造成闪退  
> * 2.对抗class-dump 和工具分析可以方法名类名混淆,混淆方案大致三种  
>   *  1）.编译前用脚本批量做宏定义替换
>   *  2）.LLVM混淆
>     *  逻辑混淆(花指令)
>   * 3）.对Mach-O__objc_classnamehe __objc_methname



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Static_obfuscation 静态混淆，使用宏进行替换字符串，或者解析mach-o中对应的section进行类名和方法名的替换 -t security
> #原来""的参数，需要自己加上""
```

