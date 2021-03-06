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

swift和c++类似，在编译期间就会确定调用方法的地址，而不需要通过oc  的runtime进行动态查找，所以效率自然提高了很多。同时swift还兼容了oc的动态特性。

> * [class-dump srouce](https://github.com/iOSHacking/class-dump)
>   * [class-dump Executable File ](https://github.com/AloneMonkey/MonkeyDev/blob/master/bin/class-dump)

#### 4.1 swift 的name mangling(名字重整)

> * name mangling 是为了解决程序实体的名字必须唯一的问题而将函数类型、函数名称、参数类型、返回类型编码到名字中的一种方法，用于从编译器中向链接器传递更多的语义信息。
>
>   * 使用nm 命令读取可执行文件的符号表
>
>     * __T014HookExampleApp14ViewControllerC14randomFunctionyyF
>
>       ```
>       nm <AppName>
>       ..
>       T __T014HookExampleApp14ViewControllerC14randomFunctionyyF
>       ..
>       nm <AppName> | xcrun swift-demangle
>       ..
>       T _HookExampleApp.ViewController.randomFunction() -> ()
>       ..
>       
>       ```
>
>       



#### 4.2 swift-demangle

swift 提供了 swift-demangle  对符号表进行解析



> * swift-demangle
>
>   * `xcrun swift-detangle -expand`
>
>   ```
>   nm <AppName>
>   ..
>   T __T014HookExampleApp14ViewControllerC14randomFunctionyyF
>   ..
>   nm <AppName> | xcrun swift-demangle
>   ..
>   T _HookExampleApp.ViewController.randomFunction() -> ()
>   ..
>   
>   ```
>
>   

#### 4.3 针对name mangling  这种情况，我们直接对该方法所在的地址使用MSHookFunction进行hook

先获取符号地址，然后直接hook

> * 1) 获取符号地址,hook 没有参数的函数
>
>   ```
>   static void (*orig_ViewController_randomFunction)(void) = NULL;
>   
>   void hook_ViewController_randomFunction() {
>      orig_ViewController_randomFunction();
>      NSLog(@"Hooked random function");
>   }
>   
>   %ctor {
>       %init(ViewController = objc_getClass("HookExampleApp.ViewController"));
>       MSHookFunction(MSFindSymbol(NULL, "__T014HookExampleApp14ViewControllerC14randomFunctionyyF"),
>                      (void*)hook_ViewController_randomFunction,
>                      (void**)&orig_ViewController_randomFunction);
>   }
>   
>   ```
>
>   
>
>   



> * [有参数的例子： swift方法的参数和oc中的方法不一样，self作为最后一个参数传递，并没有selector。](https://github.com/AloneMonkey/iOSREBook/blob/master/chapter-6/6.4-Hook/HookSwift/Tweak.xm)
>
>   ```
>   #include <substrate.h>
>   #include <objc/runtime.h>
>   
>   static int (*origin_custom_method)(int,id) = NULL;
>   
>   int new_custom_method(int number, id _self)
>   {
>   	NSLog(@"Hooked!!!");
>   	number = 20;
>       return origin_custom_method(number, _self);   
>   }
>   
>   __attribute__((constructor))
>   int main(void){
>   	NSLog(@"Load!!!");
>       MSHookFunction(MSFindSymbol(NULL,"__T09SwiftDemo14ViewControllerC12CustomMethodS2i6number_tF"),
>            (void*)new_custom_method,
>            (void**)&origin_custom_method);
>      
>      return 0;
>   }
>   
>   ```
>
>   



# See Also 

>* [cydiarepo](https://github.com/zhangkn/cydiarepo)
>* [MonkeyDev-Xcode-Templates](https://github.com/zhangkn/MonkeyDev-Xcode-Templates)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost hook_methods_in_ios Common hook methods in iOS -t hook
> #原来""的参数，需要自己加上""
```

