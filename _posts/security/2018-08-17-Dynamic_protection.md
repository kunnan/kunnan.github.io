---
layout: post
title: Dynamic_protection
date: 2018-08-17
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 反调试、反反调试、反注入、hook检测、完整性校验
---

# 前言



加解密方法、动态传参的分析就依赖动态调试去分析动态解密之后的内容、方法传递的参数。

#    反调试[AntiDebug.](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/DynamicProtect/DynamicProtect/AntiDebug.h)

> * [code for antiDebug](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/DynamicProtect/DynamicProtect/AntiDebug.m)
>
>   * 阻止调试器附加
>
>     * [anti-anti-debugging的例子](https://github.com/zhangkn/KNAlipayWalletTweakDemo/blob/master/AlipayWalletTweakF/AlipayWalletTweakF.xm)
>       * [Anti ptrace 一种反调试](https://everettjf.github.io/2015/12/20/amap-ios-client-kill-anti-debugging-protect/)
>     * [简单的 AntiDebugging 和 AntiAntiDebugging](https://everettjf.github.io/2015/12/28/simple-ios-antidebugging-and-antiantidebugging/)
>
>     
>
>   * 检测调试器是否存在

#### ptrace

> * 为了方便应用软件的开发和调试，unix的早期版本提供了一种对运行中的进程进行跟踪和控制手段：系统调用ptrace;通过ptrace,可以对另一个进程实现调试跟踪，同时ptrace提供了一个`PT_DENY_ATTACH = 31`参数用于告诉系统阻止调试器的依附。
>
>   *   [anti ptrace code]
>
>     * 直接hook ptrace
>
>       ```
>       static int (*oldptrace)(int request, pid_t pid, caddr_t addr, int data);
>       static int newptrace(int request, pid_t pid, caddr_t addr, int data){
>       	return 0; // just return zero
>       /*
>       	// or return oldptrace with request -1
>       	if (request == 31) {
>       		request = -1;
>       	}
>       	return oldptrace(request,pid,addr,data);
>       */
>       }
>       
>       
>       %ctor {
>       	MSHookFunction((void *)MSFindSymbol(NULL,"_ptrace"), (void *)newptrace, (void **)&oldptrace);
>       }
>       
>       ```
>
>       
>
>     * 返回一个假的ptrace函数
>
>       
>
>       ```
>       #import <mach-o/dyld.h>
>       #import <dlfcn.h>
>       void (*old_sub_bf92)(void);
>       
>       void new_sub_bf92(void)
>       {
>           // old_sub_bf92();
>           NSLog(@"KNiOSRE: anti-anti-debugging");
>       }
>       //sub_bf92
>       %ctor
>       {
>           @autoreleasepool
>           {
>               unsigned long _sub_bf92 = (_dyld_get_image_vmaddr_slide(0) + 0xbf92) | 0x1;
>               if (_sub_bf92) NSLog(@"KNiOSRE: Found sub_bf92!");
>                   MSHookFunction((void *)_sub_bf92, (void *)&new_sub_bf92, (void **)&old_sub_bf92);
>           }
>       }
>       
>       ```
>
>       
>
>   * [ptrace code]
>
>     
>
>     * 方式一
>
>       ```
>       #import <mach-o/dyld.h>
>       #import <dlfcn.h>
>       
>       int main(int argc, char * argv[]) {
>       
>       #ifndef DEBUG
>           typedef int (*ptrace_type)(int request, pid_t pid,caddr_t addr,int data);
>           void *handle = dlopen(0, 0xA);
>           ptrace_type pt = (ptrace_type)dlsym(handle, "ptrace");
>           pt(31,0,0,0);
>           dlclose(handle);
>       #endif
>       
>       	//...
>       }	
>       ```
>
>       
>
>     * 方式二
>
>     ```
>     #ifndef PT_DENY_ATTACH
>         #define PT_DENY_ATTACH 31
>     #endif
>     typedef int (*ptrace_ptr_t)(int _request, pid_t _pid, caddr_t _addr, int _data);
>     
>     void AntiDebug_002(){
>         void *handle = dlopen(0, RTLD_GLOBAL | RTLD_NOW);
>         ptrace_ptr_t ptrace_ptr = (ptrace_ptr_t)dlsym(handle, "ptrace");
>         ptrace_ptr(PT_DENY_ATTACH, 0, 0, 0);
>     }
>     
>     ```
>
>     

> * 小结
>
>   * 当程序运行后，使用 `debugserver *:1234 -a BinaryName` 附加进程出现 `segmentfault 11` 时，一般说明程序内部调用了ptrace 。
>   * 为验证是否调用了ptrace 可以 `debugserver -x backboard *:1234 /BinaryPath`（这里是完整路径），然后下符号断点 `b ptrace`，`c` 之后看ptrace第一行代码的位置，然后 `p $lr` 找到函数返回地址，再根据 `image list -o -f` 的ASLR偏移，计算出原始地址。最后在 IDA 中找到调用ptrace的代码，分析如何调用的ptrace
>   * 开始hook ptrace。
>
>    
>
>    



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Dynamic_protection 反调试、反反调试、反注入、hook检测、完整性校验 -t security
> #原来""的参数，需要自己加上""
```

