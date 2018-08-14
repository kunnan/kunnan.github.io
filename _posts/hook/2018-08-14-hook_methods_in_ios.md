---
layout: post
title: hook_methods_in_ios
date: 2018-08-14
tag: hook
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Common hook methods in iOS
---



# 前言

> * iOS    中常见的hook方式
>
>   * [hooking-swift-methods](https://kunnan.github.io/2018/06/06/hooking-swift-methods/)
>
>   * [Hooking & Executing Code with dlopen & dlsym ---Easy mode:hooking C methods](https://github.com/zhangkn/hookingCmethods)
>
>   * 利用[运行时AP](https://kunnan.github.io/tags/#Runtime)I ，Method Swizzling ：  通过修改oc 函数的IMP达到替换方法实现的目的
>
>   * [fishhook：A library that enables dynamically rebinding symbols in Mach-O binaries running on iOS.](https://github.com/facebook/fishhook) 通过修改内存中懒加载和非懒加载符号表指针所指向的地址来达到修改方法的目的，作用于主模块懒加载和非懒加载表的符号，在越狱和非越狱环境都可以使用。
>
>      * facebook 开源的一个非常小的重新绑定动态符号的库
>
>      * How it works
>
>         ![image](https://wx3.sinaimg.cn/large/af39b376gy1fu976jnsf3j20jo0pc76u.jpg)
>
>   * [substitute:A free runtime modification library.](https://github.com/coolstar/substitute)
>
>   * cydia substrate: `通过inline hook的方式修改目标函数内存中的汇编指令，使其调转到自己的代码块，以达到修改程序的目的，同时支持method swizzle`
>
>     * ` __attribute__((constructor)) `
>
>       ```
>       static __attribute__((constructor)) void myinit() 
>       ```
>
>       
>
>     * ios11 使用Electra 越狱之后，存放dylib的path: `/usr/lib/TweakInject`
>
>       
>
>       * [docs/getting-started.md](https://github.com/coolstar/electra/blob/master/docs/getting-started.md)
>
>         * Substitute is used as the hooking framework instead of substrate
>           
>
>       * [Electra](Electra iOS 11.0 - 11.1.2 jailbreak toolkit based on async_awake)
>
>       * [electra1131](https://github.com/coolstar/electra1131)
>
>       * [electra-ipas](https://github.com/coolstar/electra-ipas)
>
>       * Framework 存放的path
>
>         ```
>             cp -r ~/Projects/_window/LatestBuild/AFNetworking.framework ~/Layout/System/Library/Frameworks/
>         
>         ```
>
>         
>
>       * 干脆给他建立个软连接算了       ` /bin/ln -s   /usr/lib/TweakInject /Library/MobileSubstrate/DynamicLibraries `
>
>         * 先备份` cp -r /Library/MobileSubstrate/DynamicLibraries ~/`，再删除，再创建ln
>
>           ```
>                # rm -rf /Library/MobileSubstrate/DynamicLibraries
>           ```
>
>
>       ​    
>     
>     * `/Library/MobileSubstrate/DynamicLibraries/`
>
>   
>
> 
>
> 





# I、[MethodSwizzling](https://github.com/hooktweaks/iOSREBook/blob/master/chapter-6/6.4-Hook/MethodSwizzling/MethodSwizzling/HookManager.m) 



# II、[fishhook](https://github.com/hooktweaks/iOSREBook/tree/master/chapter-6/6.4-Hook/FishHook/FishHook/fishhook)



# III、[cydiasubstrate](https://github.com/hooktweaks/iOSREBook/blob/master/chapter-6/6.4-Hook/cydiasubstrate/Tweak.xm)

cydiasubstrate 提供了针对oc的runtime hook和针对c c++函数的inline hook 

#### 3.1 inline hook

> * 对open 函数进行了inline hook 

```
#include <substrate.h>
#include <objc/runtime.h>

@class ViewController;

static void (*originMethodImp)(ViewController*, SEL, id);

static void newMethodImp(ViewController* self, SEL _cmd, id sender) { 
	NSLog(@"-[<ViewController: %p> Hook clickMe:%@]", self, sender); 
	originMethodImp(self, _cmd, sender); 
}

int (*oldopen)(const char *, int, ...);

int newopen(const char *path, int oflag, ...) {
    va_list ap = {0};
    mode_t mode = 0;
    
    if ((oflag & O_CREAT) != 0) {
        va_start(ap, oflag);
        mode = va_arg(ap, int);
        va_end(ap);
        printf("MSHookFunction | Calling real open('%s', %d, %d)\n", path, oflag, mode);
        return oldopen(path, oflag, mode);
    } else {
        printf("MSHookFunction | Calling real open('%s', %d)\n", path, oflag);
        return oldopen(path, oflag, mode);
    }
}

static __attribute__((constructor)) void myinit() {

    Class targetClass = objc_getClass("ViewController");
    
	MSHookMessageEx(targetClass,@selector(clickMe:),(IMP)&newMethodImp,(IMP*)&originMethodImp);

	MSHookFunction(open, newopen, &oldopen);

}

```



# IV 、 [swifthook](https://github.com/hooktweaks/iOSREBook/blob/master/chapter-6/6.4-Hook/HookSwift/Tweak.xm) : 使用MSHookFunction、MSFindSymbol

> * [hooking-swift-methods](https://kunnan.github.io/2018/06/06/hooking-swift-methods/)

swift和c++类似，在编译期间就会确定调用方法的地址，而不需要通过oc  的runtime进行动态查找，所以效率自然提高了很多。



# See Also 

>* [cydiarepo](https://github.com/zhangkn/cydiarepo)
>* [MonkeyDev-Xcode-Templates](https://github.com/zhangkn/MonkeyDev-Xcode-Templates)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost hook_methods_in_ios Common hook methods in iOS -t hook
> #原来""的参数，需要自己加上""
```

