---
layout: post
title: NSTask_posix_spawn_popen
date: 2018-05-13
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: NSTask、posix_spawn、popen 的应用例子
---



# 前言

>* 本文以执行shell 为例子,从`#include <spawn.h>` 的popen方法为线索
>```objc
>FILE	*popen(const char *, const char *) __DARWIN_ALIAS_STARTING(__MAC_10_6, __IPHONE_2_0, __DARWIN_ALIAS(popen)) __swift_unavailable_on("Use posix_spawn APIs or NSTask instead.", "Process spawning is unavailable.");
>```


# I、popen

>* popen
>```objc
>	    // popen(@"killall -9  WeChat", @"r");//#include <stdio.h>
>```


# II、 NSTask

>* /Users/devzkn/code/github/gnustep-base/Source/NSTask.m 
>```
/Users/devzkn/code/github/gnustep-base/Headers/Foundation/NSTask.h 
```

>* `launchedTaskWithLaunchPath: arguments:`  & `kndoShellCmd:`
><script src="https://gist.github.com/zhangkn/54f36b453f68a2d2c5e5f3417a80469c.js"></script>





# III、posix_spawn

#### 3.1 posix_spawn

>*  `status = posix_spawn(&pid, "/bin/sh", NULL, NULL, argv, (char **)&myenviron);`
><script src="https://gist.github.com/zhangkn/5a87995169b126dedea2cb0abbf61af7.js"></script>
>*    ` posix_spawnp(&pid, path, &fact, &attr, args, environ);`
>```objc
>// 方式一	
>run("killall -9  WeChat");//
>//方式二
>	NSArray* args = @[@"-9",@"WeChat"];
>[KNnoZombieRunobjc Run:@"killall" withParams:args];//#include <spawn.h>
>```




# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost NSTask_posix_spawn_popen NSTask、posix_spawn、popen 的应用例子 -t objc
> #原来""的参数，需要自己加上""
```

