---
layout: post
title: String_encryption
date: 2018-08-17
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 字符串加密,使用clang-c接口将源代码转换成抽象语法树，并对抽象语法树进行遍历和分析，帮助我们分析代码中存在的字符串；进而对其中的字符串进行加密处理，来防止静态分析
---



#  前言

#### [libclang: C Interface to Clang ](https://clang.llvm.org/doxygen/group__CINDEX.html)



> * [details](https://clang.llvm.org/doxygen/group__CINDEX.html#details)
>
>   ![image](https://ws4.sinaimg.cn/large/af39b376gy1fucjqbyo2oj20bk0wldhu.jpg)

#### [拿到字符串的开始和结束的位置，然后再进行字符串加密操作](https://github.com/zhangkn/KNParseClangLib)



# 字符串加密



> * [解析字符串的code](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-8/8.1%20%E6%95%B0%E6%8D%AE%E5%8A%A0%E5%AF%86/LibClangParse/LibClangParse/main.m)
>
>   * 导入一些必要的头文件[clang-c](https://github.com/AloneMonkey/iOSREBook/tree/master/chapter-8/8.1%20%E6%95%B0%E6%8D%AE%E5%8A%A0%E5%AF%86/LibClangParse/LibClangParse/clang-c)
>
>   * ​    先使用dlopen 将其加载到内存并获取需要使用的函数指针
>
>     ```
>             void *hand = dlopen("/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/libclang.dylib",RTLD_LAZY);
>     
>     ```
>
>     

> * [另外一个解析字符串的code](https://github.com/zhangkn/KNParseClangLib)
>
>   

#### 解析字符串的代码分析

###### [clang-c/Index.h](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.1%20%E6%95%B0%E6%8D%AE%E5%8A%A0%E5%AF%86/LibClangParse/LibClangParse/clang-c/Index.h)

> * `enum CXChildVisitResult printVisitor(CXCursor cursor, CXCursor parent, CXClientData client_data) {`: \brief Visitor invoked for each cursor found by a traversal.
>
>   
>
>   * CXChildVisitResult
>     * CXChildVisit_Break
>     * CXChildVisit_Continue
>     * CXChildVisit_Recurse

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost String_encryption 字符串加密,使用clang-c接口将源代码转换成抽象语法树，并对抽象语法树进行遍历和分析，帮助我们分析代码中存在的字符串；进而对其中的字符串进行加密处理，来防止静态分析 -t security
> #原来""的参数，需要自己加上""
```

