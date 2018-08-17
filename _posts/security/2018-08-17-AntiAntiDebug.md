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



> * [AntiAntiDebugTweak.xm](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/AntiAntiDebugTweak/AntiAntiDebugTweak/AntiAntiDebugTweak.xm)
>
>   ```
>   %ctor{
>       MSHookFunction((void *)MSFindSymbol(NULL,"_ptrace"),(void*)my_ptrace,(void**)&orig_ptrace);
>       MSHookFunction((void *)sysctl,(void*)my_sysctl,(void**)&orig_sysctl);
>       MSHookFunction((void *)syscall,(void*)my_syscall,(void**)&orig_syscall);
>       NSLog(@"[AntiAntiDebug] Module loaded!!!");
>   }
>   
>   ```
>
>   



# 非越狱hook

非越狱hook的原理和越狱hook的一样，只是将hook的工具修改为fishhook.

> 
>
> * [AntiAntiDebug.m](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/AntiAntiDebug/AntiAntiDebug.m)
>
> * [AntiAntiDebug.m](https://github.com/AloneMonkey/MonkeyDev-Xcode-Templates/blob/master/MonkeyAppLibrary.xctemplate/AntiAntiDebug/AntiAntiDebug.m) 此代码已经集成在[MonkeyAppLibrary.xctemplate](https://github.com/AloneMonkey/MonkeyDev-Xcode-Templates/tree/master/MonkeyAppLibrary.xctemplate)
>
>   
>
>   ```
>   __attribute__((constructor)) static void entry(){
>       NSLog(@"[AntiAntiDebug Init]");
>       
>       rebind_symbols(
>       (struct rebinding[1])
>       {
>       {"ptrace", my_ptrace, (void*)&orig_ptrace}
>       },1);
>       
>       rebind_symbols((struct rebinding[1]){
>       {"dlsym", my_dlsym, (void*)&orig_dlsym}
>       },1);
>       
>       //some app will crash with _dyld_debugger_notification
>       // rebind_symbols((struct rebinding[1]){
>       {"sysctl", my_sysctl, (void*)&orig_sysctl}
>       },1);
>       
>       rebind_symbols((struct rebinding[1]){
>       {"syscall", my_syscall, (void*)&orig_syscall}
>       },1);
>   }
>   
>   
>   ```
>
>   

# [AntiAntiDebug.py](https://github.com/AloneMonkey/iOSREBook/blob/6dd028fea7d9ec9376cde5cc51de93f53fe5a20d/chapter-8/8.3%20%E5%8A%A8%E6%80%81%E4%BF%9D%E6%8A%A4/AntiAntiDebug/AntiAntiDebug.py) 反反调试脚本;如果有定时器定时检测，建议写tweak(越狱hook、非越狱hook)



> * [Debug](https://kunnan.github.io/tags/#Debug)
>
>   * [Chisel_fblldb.py_lldbinit](https://kunnan.github.io/2018/07/07/Chisel_fblldb.py_lldbinit/) : lldb 会默认从`~/.lldbinit `加载自定义脚本。新增command script之后，重启Xcode，或者直接在lldb交互模式下输入`command source ~/.lldbinit`来加载脚本
>
>     * 两个开源库：`[DerekSelander](https://github.com/DerekSelander)/**LLDB**`、`Chisel`
>
>       
>
>       > 
>       >
>       > - [Chisel is a collection of LLDB commands to assist debugging iOS apps.](https://github.com/zhangkn/chisel)
>       >
>       >   ```
>       >   brew update
>       >   brew install chisel
>       >   ```
>       >
>       >   ```
>       >   Add the following line to ~/.lldbinit to load chisel when Xcode launches:
>       >     command script import /usr/local/opt/chisel/libexec/fblldb.py
>       >   ```
>       >
>       > - [A collection of LLDB aliases/regexes and Python scripts to aid in your debugging sessions](https://github.com/DerekSelander/LLDB)
>       >
>       >   ```
>       >   ➜  lldb git clone git@github.com:DerekSelander/LLDB.git
>       >   Add the following command to your ~/.lldbinit file: command script import /path/to/lldb_commands/dslldb.py
>       >   ```









# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost AntiAntiDebug 反反调试：针对ptrace\sysctl\syscall,采用hook函数-》判断函数-〉返回结果的流程；hook的方式有越狱环境的hook、非越狱环境的hook以及在lldb的时候采用python脚本自动在目标函数设置条件断点，在断点的回调中改变对应的寄存器。 -t security
> #原来""的参数，需要自己加上""
```

