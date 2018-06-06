---
layout: post
title: "` __attribute__`"
date: 2018-06-05
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle:  "`__attribute__((constructor))`在`main()` 之前执行,`__attribute__((destructor))` 在`main()`执行结束之后执行"
---

# I 、`__attribute__`

>* hook 的代码在main()执行结束之前或者之后执行(解释一下：`__attribute__`((constructor)) 在main() 之前执行,`__attribute__`((destructor)) 在main()执行结束之后执行.) ------- 可以用来hook 守护进程（因此不用关心具体的方法）。

```
//constructor   在main之前 
%ctor {
    setuid(0); //设置超级用户
}
 等价于
static __attribute__((constructor)) void _logosLocalCtor_f35a0c0f(int __unused argc, char __unused **argv, char __unused **envp) {

}
%dtor { … }
```

>* 设置constructor优先级

```
//声明
__attribute__((constructor(101))) void before1();

//实现
void before1()
{
    printf("before1\n");
}
```


# II 、经典的用法



```
#include <stdio.h>
#include <stdlib.h>
 
static  __attribute__((constructor)) void before()
{
 
    printf("Hello");
}
 
static  __attribute__((destructor)) void after()
{
    printf(" World!\n");
}
 
int main(int args,char ** argv)
{
 
    return EXIT_SUCCESS;
}
```
 

# III、test.m

```
static void __attribute__((destructor)) back_main1(void)
{
printf("Come to %s\n", __func__);
}
```




#### 3.1 attribute((constructor(PRIORITY))) 和 attribute((destructor(PRIORITY)))


>* 1、PRIORITY 是指执行的优先级，main 函数执行之前会执行 constructor，main 函数执行后会执行 destructor，+load 会比 constructor 执行的更早点，因为动态链接器加载 Mach-O 文件时会先加载每个类，需要 +load 调用 之后，然后才会调用所有的 constructor 方法。

>* 2、通过这个特性，可以做些比较好玩的事情，比如说类已经 load 完了，是不是可以在 constructor 中对想替换的类进行替换，而不用加在特定类的 +load 方法里。

#### 3.2 具体的例子

>* 3.2.1 我常用来执行一次性代码`KNHook `
```
__attribute__((constructor)) static void before1()
{
    printf("beforeFunction\n");
	[KNHook hookClass:@"WebViewJavascriptBridge"];
}
```

>* 3.2.2 JSPatch

```
<!-- jsPatch.mm -->

//需要hook的方法


#import <JSPatchPlatform/JSPatch.h>


CHDeclareClass(MicroMessengerAppDelegate);

////JSPatch初始化

CHMethod(2, BOOL, MicroMessengerAppDelegate, application, id, arg1, didFinishLaunchingWithOptions, id, arg2)
{
    BOOL supBool = CHSuper(2, MicroMessengerAppDelegate, application, arg1, didFinishLaunchingWithOptions, arg2);
    [JSPatch startWithAppKey:@"xxx"];
    [JSPatch sync];
    return supBool;
}

//所有被hook的类和函数放在这里的构造函数中

__attribute__((constructor)) static void entry()
{
    CHLoadLateClass(MicroMessengerAppDelegate);
    CHHook(2, MicroMessengerAppDelegate, application, didFinishLaunchingWithOptions);
}
```

>* 3.2.3 test.dylib

```
// xcrun -sdk iphoneos gcc -dynamiclib -arch arm64 -framework Foundation -o test.dylib test.m
// jtool --sign --inplace test.dylib

#include <dlfcn.h>
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <mach/mach.h>
#include <mach-o/loader.h>
#include <mach/error.h>
#include <errno.h>
#include <stdlib.h>
#include <sys/sysctl.h>
#include <dlfcn.h>
#include <sys/mman.h>
#include <spawn.h>
#include <sys/stat.h>
#include <pthread.h>
#include <xpc/xpc.h>

#import <Foundation/Foundation.h>

__attribute__ ((constructor))
static void ctor(void) {
    NSLog(@"Unsigned dylib!!");
}
```

# See Also 

- [__ATTRIBUTE__ 你知多少？](http://www.cnblogs.com/astwish/p/3460618.html)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost __attribute__ __attribute__((constructor)) 在main() 之前执行,__attribute__((destructor)) 在main()执行结束之后执行 -t iosre
> #原来""的参数，需要自己加上""
```

