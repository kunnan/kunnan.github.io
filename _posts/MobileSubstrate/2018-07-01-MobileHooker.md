---
layout: post
title: MobileHooker
date: 2018-07-01
tag: MobileSubstrate
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 替换系统和应用的方法
---



# MobileHooker



```
IMP MSHookMessage(Class class, SEL selector, IMP replacement, const char* prefix); // prefix should be NULL.
void MSHookMessageEx(Class class, SEL selector, IMP replacement, IMP *result);
void MSHookFunction(void* function, void* replacement, void** p_original);
```



> * MSHookMessage() will replace the implementation of the Objective-C message `-[`*class* *selector*`]` by *replacement*, and return the original implementation. To hook a class method, provide the meta class retrieved from objc_getMetaClass in the MSHookeMessage(Ex) call  This dynamic replacement is in fact a feature of Objective-C, and can be done using [method_setImplementation](http://developer.apple.com/mac/library/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/c/func/method_setImplementation). MSHookMessage() is not thread-safe and has been deprecated in favor of MSHookMessageEx()
>
> * `MSHookFunction() `is like MSHookMessage() but is for C/C++ functions. The replacement is done at assembly level. Conceptually, MSHookFunction() will write instructions that jumps to the replacement function, and allocate some bytes on a custom memory location, which has the original cut-out instructions and a jump to the rest of the hooked function. Since on iOS by default a memory page cannot be simultaneously writable and executable, a kernel patch must be applied for MSHookFunction() to work. (Any public jailbreak should have one.)
>
>    



# See Also 

>* [MobileHooker](http://iphonedevwiki.net/index.php/Cydia_Substrate#MobileHooker)
>* [iOSSecurity-Attack](https://github.com/githubPagesio/iOSSecurity-Attack)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost MobileHooker 替换系统和应用的方法 -t MobileSubstrate
> #原来""的参数，需要自己加上""
```

