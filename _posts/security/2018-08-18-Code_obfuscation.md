---
layout: post
title: Code_obfuscation
date: 2018-08-18
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 代码混淆
---

# 前言

>   



在编译器处理分析生成代码的时候，来 增加一些无用的代码、拆分代码块、使代码扁平化，进而提升静态分析的难度。

Chris Lattner 生于 1978 年，2005年加入苹果，将苹果使用的 GCC 全面转为 LLVM。2010年开始主导开发 Swift 语言。  

> * Xcode的编译器前端是clang，clang是llvm的一部分；而llvm本身是开源的，基于llvm 进行代码混淆的工具中，本人最终喜欢的马甲包混淆方案是用[`Hikari`](https://github.com/HikariObfuscator/Hikari),具体的用法看[这里](https://kunnan.github.io/2018/06/05/iosObfuscation/)
>   * Clang 是 LLVM 的子项目，是 C，C++ 和 Objective-C 编译器 http://clang.llvm.org/docs/
>     * 其中的 clang static analyzer 主要是进行语法分析，语义分析和生成中间代码，当然这个过程会对代码进行检查，出错的和需要警告的会标注出来。
>     * lld 是 Clang / LLVM 的内置链接器，clang 必须调用链接器来产生可执行文件。 

> *  编译器处理过程
>
>   * 1、预处理  
>
>     * 符号化 (Tokenization)  
>     * 宏定义的展开  
>     * #include 的展开  
>     * 语法和语义分析  
>
>   * 2、将符号化后的内容转化为一棵解析树 (parse tree)  
>
>     * 解析树做语义分析  
>     * 输出一棵抽象语法树（Abstract Syntax Tree* (AST)）  
>     * 生成代码和优化  
>
>   * 3、将 AST 转换为更低级的中间码 (LLVM IR)  
>
>     * 对生成的中间码做优化  
>       * LLVM IR 有三种表示格式，第一种是 bitcode 这样的存储格式，以 .bc 做后缀，第二种是可读的以 .ll，第三种是用于开发时操作 LLVM IR 的内存格式
>     * 生成特定目标代码  
>     * 输出汇编代码  
>
>     
>
>   * 4、汇编器  
>
>     * 将汇编代码转换为目标对象文件。  
>
>   * 5、链接器  
>
>     * 将多个目标对象文件合并为一个可执行文件 (或者一个动态库)  

# 什么是LLVM？

llvm 是一系列 



# See Also 

>* https://zhangkn.github.io/2018/03/Hopper/
>  * LLVM IR 有三种表示格式，第一种是 bitcode 这样的存储格式，以 .bc 做后缀，第二种是可读的以 .ll，第三种是用于开发时操作 LLVM IR 的内存格式
>  * 当虚拟内存系统进行映射时，segment 和 section 会以不同的参数和权限被映射。  
>    * __TEXT segment 包含了被执行的代码。它被以只读和可执行的方式映射  
>    * __DATA segment 以可读写和不可执行的方式映射。它包含了将会被更改的数据。  
>    * __nl_symbol_ptr 和 __la_symbol_ptr，它们分别是 non-lazy 和 lazy 符号指针。  
>      * 延迟符号指针用于可执行文件中调用未定义的函数，例如不包含在可执行文件中的函数，它们将会延迟加载。而针对非延迟符号指针，当可执行文件被加载同时，也会被加载。  
>* [深入剖析 iOS 编译 Clang / LLVM 视频](https://segmentfault.com/l/1500000008514518)
>* [深入剖析 iOS 编译 Clang / LLVM 文章 Low Level Virtual Machine](https://ming1016.github.io/2017/03/01/deeply-analyse-llvm/)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Code_obfuscation 代码混淆 -t security
> #原来""的参数，需要自己加上""
```

