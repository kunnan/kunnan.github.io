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
>     * [**ios-class-guard**:Simple Objective-C obfuscator for Mach-O executables](https://github.com/Polidea/ios-class-guard)
>   * 修改二进制文件对应的section



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Static_obfuscation 静态混淆，使用宏进行替换字符串，或者解析mach-o中对应的section进行类名和方法名的替换 -t security
> #原来""的参数，需要自己加上""
```

