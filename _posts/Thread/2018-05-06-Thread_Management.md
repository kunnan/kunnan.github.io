---
layout: post
title: Thread_Management
date: 2018-05-06
tag: Thread
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: an overview of the thread technologies available in OS X and iOS along with examples of how to use those technologies in your applications
---




# I、 多线程


>*  Your application does not need to create these objects explicitly; 
>```
>1)each thread, including the application’s main thread, has an associated run loop object. 
>2)Only secondary threads need to run their run loop explicitly, however. The app frameworks automatically set up and run the run loop on the main thread as part of the application startup proces
>```


#### 1、1 pthread_t

>* pthread_t 的定义和演示
><script src="https://gist.github.com/zhangkn/47240bde3df9d3b2b6b76ce6cd6d1338.js"></script>


#### 1、2 NSThread


一个NSThread对象就代表一条线程

>*  [创建、启动NSThread线程](https://gist.github.com/zhangkn/3e265030afdd521faed4d7a152666dc0)
><script src="https://gist.github.com/zhangkn/3e265030afdd521faed4d7a152666dc0.js"></script>
>

>* 线程的状态
>![](https://ws4.sinaimg.cn/large/006tKfTcgy1fqza209j8tj30w20gpq4u.jpg)

>* [控制线程状态](https://gist.github.com/zhangkn/fca416497b52eabfe260f7a898b91436)
><script src="https://gist.github.com/zhangkn/fca416497b52eabfe260f7a898b91436.js"></script>

>* [原子和非原子属性](https://gist.github.com/zhangkn/cd4f098321241830d3d7e1601a9ddd3a)
><script src="https://gist.github.com/zhangkn/cd4f098321241830d3d7e1601a9ddd3a.js"></script>
>
>* [线程间通信](https://zhangkn.github.io/2018/01/Inter-processCommunicationByRrocketbootstrap/)
>
>

#### 1、3 GCD（Grand Central Dispatch）

>* 纯C语言，提供了非常多强大的函数。GCD是苹果公司为多核的并行运算提出的解决方案
>```
>GCD会自动利用更多的CPU内核（比如双核、四核）
>GCD会自动管理线程的生命周期（创建线程、调度任务、销毁线程）
>程序员只需要告诉GCD想要执行什么任务，不需要编写任何线程管理代码
>```

###### 1、3、1 任务和队列

>* GCD中有2个核心概念
>```
任务：执行什么操作
队列：用来存放任务
```

>* GCD的使用就2个步骤
>```
1)定制任务:确定想做的事情
2) 将任务添加到队列中: 
GCD会自动将队列中的任务取出，放到对应的线程中执行
任务的取出遵循队列的FIFO原则：先进先出，后进后出
```
>* [执行任务](https://gist.github.com/zhangkn/5202b338dee49adbf373fb7efaf0b33c)
><script src="https://gist.github.com/zhangkn/5202b338dee49adbf373fb7efaf0b33c.js"></script>
>

>* [队列的类型：Concurrent Dispatch Queue、Serial Dispatch Queue](https://gist.github.com/zhangkn/3e24418985f4895c41fd3a5fc0162bc4)
><script src="https://gist.github.com/zhangkn/3e24418985f4895c41fd3a5fc0162bc4.js"></script>
>
>* 各种队列的执行效果
>![](https://ws2.sinaimg.cn/large/006tKfTcly1fqzbb4cazdj30w40fb75g.jpg)
>* [线程间通信示例](https://gist.github.com/zhangkn/37a271df3bbd39860f9476ab92843707)
><script src="https://gist.github.com/zhangkn/37a271df3bbd39860f9476ab92843707.js"></script>
>
>* 延时执行
><script src="https://gist.github.com/zhangkn/1600550a7f70b86dd951e3efd0fbea18.js"></script>
>* 一次性代码
><script src="https://gist.github.com/zhangkn/3c37b8db36f2086d12d0142315f7c39e.js"></script>
>* [队列组:等2个异步操作都执行完毕后，再回到主线程执行操作](https://gist.github.com/zhangkn/91ba86f7ace93e7c744d437fafc4d1e5)
><script src="https://gist.github.com/zhangkn/91ba86f7ace93e7c744d437fafc4d1e5.js"></script>
>* [单例模式](https://github.com/zhangkn/KNIosCommonTool/blob/master/KNIosCommonTool/Classes/PublicInterface/HSSingleton.h)
>```
>pod KNIosCommonTool
>```
><script src="https://gist.github.com/zhangkn/9f90f6e4f32e104a18b3473b2e40cf4f.js"></script>
>


#### 1.4 NSOperation

>* 简介
><script src="https://gist.github.com/zhangkn/e9329307c6157ffbee887c31b68c1e71.js"></script>
>

###### 1.4.0  NSOperation的子类

>* NSOperation的子类
><script src="https://gist.github.com/zhangkn/773fc2ac01f3bca0c3fd0e3e732d35bc.js"></script>
>
>* 1) NSInvocationOperation
>*<script src="https://gist.github.com/zhangkn/df65374e11c7b5faf59788aa6c9609cf.js"></script>
>* 2) NSBlockOperation
><script src="https://gist.github.com/zhangkn/2cc795f85b78906cd9de9190440b04f4.js"></script>
>* 3)自定义子类继承NSOperation，实现内部相应
><script src="https://gist.github.com/zhangkn/320cd4b419773bdcc4b4ba694acbb682.js"></script>
>* NSOperationQueue
><script src="https://gist.github.com/zhangkn/368499394d23c40d7f9f1e294dcd0386.js"></script>
>


###### 1.4.1 NSOperation的方法

>* 操作优先级
><script src="https://gist.github.com/zhangkn/f6d9dc91b3e352ef770c7ad2d5c204a2.js"></script>
>* 操作的监听
><script src="https://gist.github.com/zhangkn/cc80dfdee09346a0c2af33d865898324.js"></script>
>* 操作依赖
><script src="https://gist.github.com/zhangkn/b1d75ca60e1031abae1891d0dd139946.js"></script>

###### 1.4.2 cell下载图片思路 – 用沙盒缓存（使用md5生成图片名称，保证唯一；或者使用url）

>* [使用operations实现下载操作](https://gist.github.com/zhangkn/711d92f07041ed0e392085cbeb588641)
>![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqzd4ef8ytj30yv0l0adj.jpg)
><script src="https://gist.github.com/zhangkn/711d92f07041ed0e392085cbeb588641.js"></script>
>* [SDWebImage](https://github.com/rs/SDWebImage)
>```
>图片下载、图片缓存、下载进度监听、gif处理
>```




# II、Thread Costs

>* The core structures needed to manage your thread and coordinate its scheduling are stored in the kernel using wired memory. 
>```
>Your thread’s stack space and per-thread data is stored in your program’s memory space. 
>Most of these structures are created and initialized when you first create the thread—a process that can be relatively expensive because of the required interactions with the kernel.
>```

#### 2.1 Thread creation costs

>* Kernel data structures
>```
>Approximately 1 KB
>```
>* Stack space
>```
>512 KB (secondary threads)
8 MB (OS X main thread)
1 MB (iOS main thread)
>```
>* Creation time
>```
>Approximately 90 microseconds
>```
>
>* [ see Concurrency Programming Guide.](https://developer.apple.com/library/content/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091)
>

# III、Creating a Thread

>* [For information on how to configure your threads, see Configuring Thread Attributes](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/10000057i-CH15-SW8)
>

#### 3.1 Using NSThread

>* There are two ways to create a thread using the NSThread class:
><script src="https://gist.github.com/zhangkn/bd318f66b45025c6419a166d80f7f96f.js"></script>
>








 


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Thread_Management an overview of the thread technologies available in OS X and iOS along with examples of how to use those technologies in your applications -t Thread
> #原来""的参数，需要自己加上""
```

