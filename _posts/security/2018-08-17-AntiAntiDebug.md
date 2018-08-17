---
layout: post
title: AntiAntiDebug
date: 2018-08-17
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 反反调试：针对ptrace\sysctl\syscall,采用hook函数-》判断函数-〉返回结果的流程；hook的方式有越狱环境的hook、非越狱环境的hook以及在lldb的时候采用python脚本自动在目标函数设置条件断点，在断点的回调中改变对应的寄存器。
---



# 越狱hook





# 非越狱hook

非越狱hook的原理和越狱hook的一样，只是将hook的工具修改为fishhook.

> 
>
> * [AntiAntiDebug.m](https://github.com/AloneMonkey/MonkeyDev-Xcode-Templates/blob/master/MonkeyAppLibrary.xctemplate/AntiAntiDebug/AntiAntiDebug.m) 此代码已经集成在[MonkeyAppLibrary.xctemplate](https://github.com/AloneMonkey/MonkeyDev-Xcode-Templates/tree/master/MonkeyAppLibrary.xctemplate)
>
>   
>
>   ```
>   __attribute__((constructor)) static void entry(){
>       NSLog(@"[AntiAntiDebug Init]");
>       
>       rebind_symbols((struct rebinding[1]){{"ptrace", my_ptrace, (void*)&orig_ptrace}},1);
>       
>       rebind_symbols((struct rebinding[1]){{"dlsym", my_dlsym, (void*)&orig_dlsym}},1);
>       
>       //some app will crash with _dyld_debugger_notification
>       // rebind_symbols((struct rebinding[1]){{"sysctl", my_sysctl, (void*)&orig_sysctl}},1);
>       
>       rebind_symbols((struct rebinding[1]){{"syscall", my_syscall, (void*)&orig_syscall}},1);
>   }
>   
>   
>   ```
>
>   



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost AntiAntiDebug 反反调试：针对ptrace\sysctl\syscall,采用hook函数-》判断函数-〉返回结果的流程；hook的方式有越狱环境的hook、非越狱环境的hook以及在lldb的时候采用python脚本自动在目标函数设置条件断点，在断点的回调中改变对应的寄存器。 -t security
> #原来""的参数，需要自己加上""
```

