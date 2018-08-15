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

# I 、动态库的编译和注入



> * 

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Dyld_Introduction 动态库： 静态库、动态库的区别，编译和注入；导出和银川符号表； -t dyld
> #原来""的参数，需要自己加上""
```

