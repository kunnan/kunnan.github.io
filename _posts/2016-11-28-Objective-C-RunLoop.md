---
layout:     post
title:      Objective-C：RunLoop
subtitle:   深入理解RunLoop
date:       2016-11-28
author:     
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - RunLoop
---



# 前言

>* Both Cocoa and Core Foundation provide run loop objects to help you configure and manage your thread’s run loop. 
>```
苹果利用 RunLoop 实现自动释放池、延迟回调、触摸事件、屏幕刷新等功能的。
```

>* [Run Loops](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW23)
>
>
>* [NSRunLoop Class Reference ](https://developer.apple.com/documentation/foundation/runloop)
>  [CFRunLoop Reference](https://developer.apple.com/documentation/corefoundation/cfrunloop)



>* [Technical Notes](https://developer.apple.com/library/content/navigation/index.html#section=Resource%20Types&topic=Technical%20Notes)
>* [Inter-processCommunicationByRrocketbootstrap](https://zhangkn.github.io/2018/01/Inter-processCommunicationByRrocketbootstrap/)



####  目录

- RunLoop 与线程的关系
- RunLoop 对外的接口
- RunLoop 的 Mode
- RunLoop 的内部逻辑
- RunLoop 的底层实现
- 苹果用 RunLoop 实现的功能
	- AutoreleasePool
	- 事件响应
	- 手势识别
	- 界面更新
	- 定时器
	- PerformSelecter
	- 关于GCD
	- 关于网络请求
- RunLoop 的实际应用举例
	- AFNetworking
	- AsyncDisplayKit
	

####  [RunLoop 的概念](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW23)


>* A run loop is an event processing loop that you use to schedule work and coordinate the receipt of incoming events.
>```
> The purpose of a run loop is to keep your thread busy when there is work to do and put your thread to sleep when there is none.
> ```

>* 如何管理事件/消息，如何让线程在没有处理消息时休眠以避免资源占用、在有消息到来时立刻被唤醒。
>```
>The programmatic interface to objects that manage input sources.
>1） RunLoop 实际上就是一个对象，这个对象管理了其需要处理的事件和消息，并提供了一个入口函数来执行Event Loop 的逻辑。
>2） 线程执行了这个函数后，就会一直处于这个函数内部 "接受消息->等待->处理" 的循环中，直到这个循环结束（激活触发退出循环的条件，函数返回。
>3） OSX/iOS 系统中，提供了两个这样的对象：CFRunLoopRef（CoreFoundation，提供了纯 C 函数的 API，所有这些 API 都是线程安全的）。
>NSRunLoop(基于 CFRunLoopRef 的封装，提供的 API不是线程安全的)
```
>* [CFRunLoopRef 的代码开源](https://opensource.apple.com/source/CF/CF-855.17/CFRunLoop.c)
>* [可以在这里](http://opensource.apple.com/tarballs/CF/)下载到整个 `CoreFoundation` 的源码来查看。
>* note: Swift 开源后，苹果又维护了[一个跨平台的 CoreFoundation 版本](https://github.com/apple/swift-corelibs-foundation/)适配了 `Linux/Windows`.

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


# II、RunLoop 与线程的关系

>* Run loops are part of the fundamental infrastructure associated with threads
>```
> A run loop is an event processing loop that you use to schedule work and coordinate the receipt of incoming events. The purpose of a run loop is to keep your thread busy when there is work to do and put your thread to sleep when there is none.
>```


>* [how Mac OS threading works](https://www.fenestrated.net/mac/mirrors/Apple%20Technotes%20(As%20of%202002)/tn/tn2028.html)
>* Mac OS X thread layering hierarchy
>![](https://ws4.sinaimg.cn/large/006tKfTcgy1fqz9jav6rjg306k05g741.gif)
>

>* CFRunLoop是基于pthread来管理的。

>* 苹果不允许直接创建 RunLoop，它只提供了两个自动获取的函数：`CFRunLoopGetMain()` 和 `CFRunLoopGetCurrent()`。
><script src="https://gist.github.com/zhangkn/17ee74e1af575bc7253562a064b6e64a.js"></script>



# III、RunLoop 对外的接口



#### 3.0 [NSRunLoop](https://developer.apple.com/documentation/foundation/runloop)

The programmatic interface to objects that manage input sources.


>* [NSRunLoop.h](https://developer.apple.com/documentation/foundation/nsrunloop?language=objc)
><script src="https://gist.github.com/zhangkn/fb417fb401bfd8f2346cc5efd7acaf6f.js"></script>
>
>

###### 3.0.1 Overview

A NSRunLoop object processes input for sources such as mouse and keyboard events from the `window system`, [`NSPort objects`](https://developer.apple.com/documentation/foundation/nsport?language=objc), and [`NSConnection objects`](https://developer.apple.com/documentation/foundation/nsconnection?language=objc). A NSRunLoop object also processes[` NSTimer events`](https://developer.apple.com/documentation/foundation/nstimer?language=objc)

>* Your application neither creates or explicitly manages NSRunLoop objects.
>```
> Each NSThread object—including the application’s main thread—has an NSRunLoop object automatically created for it as needed. If you need to access the current thread’s run loop, you do so with the class method currentRunLoop.
>```
>* Note that from the perspective of NSRunLoop
>```
> NSTimer objects are not "input"—they are a special type, and one of the things that means is that they do not cause the run loop to return when they fire.
>```


>* Warning: The `NSRunLoop `class is generally not considered to be thread-safe and its methods should only be called within the context of the current thread.
>
>

###### 3.0.2 [Topics](https://developer.apple.com/documentation/foundation/nsrunloop?language=objc)

>* Accessing Run Loops and Modes
><script src="https://gist.github.com/zhangkn/6e6f0f5867ede44bdfaac2ee6c655323.js"></script>
>* Managing Timers
>```
>1)addTimer:forMode:
>Registers a given timer with a given input mode.
>2) Discussion:
>You can add a timer to multiple input modes
>```
>* Managing Ports
><script src="https://gist.github.com/zhangkn/c270c28bed01f6e1109a1d3270ca2643.js"></script>
>* Running a Loop
><script src="https://gist.github.com/zhangkn/d3d3b868caba3677dc179a68259a1c06.js"></script>
>
>* Scheduling and Canceling Messages
><script src="https://gist.github.com/zhangkn/e22f6c0f1ad9409273fd5f9f832476f1.js"></script>
>* Constants
><script src="https://gist.github.com/zhangkn/48f955982b1fc18a277cf6bb6c6e4211.js"></script>
>Instance Methods
>```
>- performBlock:
- performInModes:block:
>```


###### 3.0.3 See Also

>* Run Loop Scheduling
>```
>NSTimer: A timer that fires after a certain time interval has elapsed, sending a specified message to a target object.
>```


#### 3.1 CFRunLoop.h

>* [CFRunLoop.c](https://github.com/apple/swift-corelibs-foundation/blob/master/CoreFoundation%2FRunLoop.subproj%2FCFRunLoop.c#L1948-L1980)
>```
>https://github.com/iossrc/swift-corelibs-foundation
>```

>* `#import <CoreFoundation/CFRunLoop.h>`关于RunLoop有5个类:
> <script src="https://gist.github.com/zhangkn/ab5698d096e394f81434b77cf82e80ec.js"></script>
> ![RunLoop 有5个类的关系](https://ws3.sinaimg.cn/large/006tKfTcgy1fqzeit62ncj30pa0jugm5.jpg)
>* `CFRunLoopSourceContext`、`CFRunLoopTimerContext`、`CFRunLoopObserverContext`struct
><script src="https://gist.github.com/zhangkn/1f437a306ac3fd0779b483a6d187dfeb.js"></script>
>```
1)一个 RunLoop 包含若干个 Mode，每个 Mode 又包含若干个 Source/Timer/Observer。
2)每次调用 RunLoop 的主函数时，只能指定其中一个 Mode，这个Mode被称作 CurrentMode。如果需要切换 Mode，只能退出 Loop，再重新指定一个 Mode 进入。---这样做主要是为了分隔开不同组的 Source/Timer/Observer，让其互不影响。
```


###### 3.1.1 CFRunLoopSourceRef---perform

>* **CFRunLoopSourceRef** 是事件产生的地方。
>```
>    void	(*perform)(void *info);//Source0手动调用 `CFRunLoopWakeUp(runloop)` 来唤醒 RunLoop，让其处理这个事件perform
>```
>* Source 有两个版本：Source0 和 Source1。
><script src="https://gist.github.com/zhangkn/16ca7977483f807e1bca2da787f1f515.js"></script>
>```
>1)Source0 只包含了一个回调（函数指针）perform，它并不能主动触发事件。使用时，你需要先调用 `CFRunLoopSourceSignal(source)`，将这个 Source 标记为待处理，然后手动调用 `CFRunLoopWakeUp(runloop)` 来唤醒 RunLoop，让其处理这个事件perform。
>2)Source1 包含了一个 mach_port 和一个回调（函数指针），被用于通过内核和其他线程相互发送消息。这种 Source 能主动唤醒 RunLoop 的线程
>```

>* [Source0的例子：运行runLoop 一次，阻塞当前线程以等待处理一次输入源](https://gist.github.com/zhangkn/2721a43436676e15c8f8be497dac9901)
><script src="https://gist.github.com/zhangkn/2721a43436676e15c8f8be497dac9901.js"></script>

>* [CFUserNotificationCreateRunLoopSource](https://developer.apple.com/documentation/corefoundation/1534507-cfusernotificationcreaterunloops?language=objc)
>


###### 3.1.2  CFRunLoopTimerRef


>* **CFRunLoopTimerRef** 是基于时间的触发器，它和 NSTimer 是toll-free bridged 的，可以混用。
>```其包含一个时间长度和一个回调（函数指针）。
>当其加入到 RunLoop 时，RunLoop会注册对应的时间点，当时间点到时，RunLoop会被唤醒以执行那个回调。
>```
><script src="https://gist.github.com/zhangkn/dc035d074cc80a31d8417e7be11bc5d8.js"></script>
>


###### 3.2.3 CFRunLoopObserverRef

>* **CFRunLoopObserverRef** 是观察者，每个 Observer 都包含了一个回调（函数指针）
><script src="https://gist.github.com/zhangkn/2d32f6bbe5a09f338355e0049334132c.js"></script>
>


###### 3.2.4 mode item

>* `Source`/`Timer`/`Observer` 被统称为 `mode item`，
>```
>一个 item 可以被同时加入多个 mode。
>但一个 item 被重复加入同一个 mode 时是不会有效果的。如果一个 mode 中一个 item 都没有，则 RunLoop 会直接退出，不进入循环
>```

>* 小结
>```
>1)一个 RunLoop 包含若干个 Mode，每个 Mode 又包含若干个 Source/Timer/Observer。
2)每次调用 RunLoop 的主函数时，只能指定其中一个 Mode，这个Mode被称作 CurrentMode。如果需要切换 Mode，只能退出 Loop，再重新指定一个 Mode 进入。---这样做主要是为了分隔开不同组的 Source/Timer/Observer，让其互不影响。
>```


# IV、 RunLoop 的 Mode

>* [swift-corelibs-foundation/CoreFoundation/RunLoop.subproj/CFRunLoop.c](https://github.com/apple/swift-corelibs-foundation/blob/386f3a5d71dc51845abddbf8c2ffb3179cd5cc89/CoreFoundation/RunLoop.subproj/CFRunLoop.c)
>* CFRunLoopMode结构
><script src="https://gist.github.com/zhangkn/acecc505d64fd8741072e0cee6195f23.js"></script>
>* CFRunLoop 的结构大致如下：
><script src="https://gist.github.com/zhangkn/6d567a95877cac9839f029c9342bf148.js"></script>


>* 重要概念`CommonModes`：
>```
>1)一个 Mode 可以将自己标记为"Common"属性（通过将其 ModeName 添加到 RunLoop 的 "commonModes" 中）。
>2)每当 RunLoop 的内容发生变化时，RunLoop 都会自动将 _commonModeItems 里的 Source/Observer/Timer 同步到具有 "Common" 标记的所有Mode里。
>```

#### 4.1 CFRunLoop对外暴露的管理 Mode 接口只有下面2个:

>* CFRunLoop对外暴露的管理 Mode 接口只有下面2个
><script src="https://gist.github.com/zhangkn/968e4509c6b3468f9ed17f35a0aebeb5.js"></script>




#### 4.2 Mode 暴露的管理 mode item 的接口有下面几个：



>* Mode 暴露的管理 mode item 的接口
><script src="https://gist.github.com/zhangkn/200ef53dff04e5265c83475c393fc1b2.js"></script>

>* 苹果公开提供的 Mode 有两个：`kCFRunLoopDefaultMode (NSDefaultRunLoopMode)` 和 `UITrackingRunLoopMode`，你可以用这两个 Mode Name 来操作其对应的 Mode。
><script src="https://gist.github.com/zhangkn/efe456e8efa85f79718f26e359a5a4cf.js"></script>


#### 4.3 同时苹果还提供了一个操作 Common 标记的字符串：

>* `kCFRunLoopCommonModes (NSRunLoopCommonModes)`，你可以用这个字符串来操作 Common Items，或标记一个 Mode 为 "Common"。使用时注意区分这个字符串和其他 mode name。
>
>


# V、RunLoop 的内部逻辑

>* Run loop management is not entirely automatic.
>``` You must still design your thread’s code to start the run loop at appropriate times and respond to incoming events
```

#### 5.1 Anatomy of a Run Loop

It is a loop your thread enters and uses to run event handlers in response to incoming events. 

###### 5.1.0 以runUntilDate: 为例子

>* runUntilDate
>```
>- (void)runUntilDate:(NSDate *)limitDate;
>```

>* [Structure of a run loop and its sources](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW23)
>![](https://ws2.sinaimg.cn/large/006tKfTcgy1fr0fbdpd89j30dg071aaa.jpg)
>* A run loop receives events from two different types of sources.
>```
>1)  Input sources deliver asynchronous events, usually messages from another thread or from a different application.
>2) Timer sources deliver synchronous events, occurring at a scheduled time or repeating interval.
>```

###### 5.1.1 以int CFRunLoopRunSpecific(runloop, modeName, seconds, stopAfterHandle) 为例子，即： CFRunLoopRun方法

>![](https://ws4.sinaimg.cn/large/006tKfTcgy1fr0fosn615j30zy0ro0wd.jpg)
> RunLoop的实现:CFRunLoopRunSpecific其内部代码整理
><script src="https://gist.github.com/zhangkn/f66b62863d0d7137ffec6e59a0896174.js"></script>
>```
>实际上CFRunLoopRun 就是这样一个函数，其内部是一个 do-while 循环。
>当你调用 `CFRunLoopRun()` 时，线程就会一直停留在这个循环里；直到超时或被手动停止，该函数才会返回。
```

# RunLoop 的底层实现

从上面代码可以看到，RunLoop 的核心是基于 mach port 的，其进入休眠时调用的函数是 `mach_msg()`。为了解释这个逻辑，下面稍微介绍一下 OSX/iOS 的系统架构。

![](http://ww4.sinaimg.cn/large/7853084cjw1fa7xzae9dlj206203nwel.jpg)

苹果官方将整个系统大致划分为上述4个层次：

应用层包括用户能接触到的图形应用，例如 Spotlight、Aqua、SpringBoard 等。

应用框架层即开发人员接触到的 Cocoa 等框架。

核心框架层包括各种核心框架、OpenGL 等内容。

Darwin 即操作系统的核心，包括系统内核、驱动、Shell 等内容，这一层是开源的，其所有源码都可以在 [opensource.apple.com](https://opensource.apple.com/) 里找到。

我们在深入看一下 Darwin 这个核心的架构：

![](http://ww4.sinaimg.cn/large/7853084cjw1fa7xzt0n5wj2070060wel.jpg)

其中，在硬件层上面的三个组成部分：Mach、BSD、IOKit (还包括一些上面没标注的内容)，共同组成了 XNU 内核。
XNU 内核的内环被称作 Mach，其作为一个微内核，仅提供了诸如处理器调度、IPC (进程间通信)等非常少量的基础服务。
BSD 层可以看作围绕 Mach 层的一个外环，其提供了诸如进程管理、文件系统和网络等功能。
IOKit 层是为设备驱动提供了一个面向对象(C++)的一个框架。

Mach 本身提供的 API 非常有限，而且苹果也不鼓励使用 Mach 的 API，但是这些API非常基础，如果没有这些API的话，其他任何工作都无法实施。在 Mach 中，所有的东西都是通过自己的对象实现的，进程、线程和虚拟内存都被称为"对象"。和其他架构不同， Mach 的对象间不能直接调用，只能通过消息传递的方式实现对象间的通信。"消息"是 Mach 中最基础的概念，消息在两个端口 (port) 之间传递，这就是 Mach 的 `IPC (进程间通信)` 的核心。

Mach 的消息定义是在 `<mach/message.h>` 头文件的，很简单：

```
typedef struct {
  mach_msg_header_t header;
  mach_msg_body_t body;
} mach_msg_base_t;
 
typedef struct {
  mach_msg_bits_t msgh_bits;
  mach_msg_size_t msgh_size;
  mach_port_t msgh_remote_port;
  mach_port_t msgh_local_port;
  mach_port_name_t msgh_voucher_port;
  mach_msg_id_t msgh_id;
} mach_msg_header_t;
```

一条 Mach 消息实际上就是一个二进制数据包 (BLOB)，其头部定义了当前端口 `local_port` 和目标端口 `remote_port`，

发送和接受消息是通过同一个 API 进行的，其 option 标记了消息传递的方向：

```
mach_msg_return_t mach_msg(
			mach_msg_header_t *msg,
			mach_msg_option_t option,
			mach_msg_size_t send_size,
			mach_msg_size_t rcv_size,
			mach_port_name_t rcv_name,
			mach_msg_timeout_t timeout,
			mach_port_name_t notify);
```

为了实现消息的发送和接收，`mach_msg()` 函数实际上是调用了一个 Mach 陷阱 (trap)，即函数 `mach_msg_trap()`，陷阱这个概念在 Mach 中等同于系统调用。当你在用户态调用 `mach_msg_trap()` 时会触发陷阱机制，切换到内核态；内核态中内核实现的 `mach_msg()` 函数会完成实际的工作，如下图：

![](http://blog.ibireme.com/wp-content/uploads/2015/05/RunLoop_5.png)

这些概念可以参考维基百科: [System_call](http://en.wikipedia.org/wiki/System_call)、[Trap_(computing)](http://en.wikipedia.org/wiki/Trap_(computing))。

RunLoop 的核心就是一个 `mach_msg()` (见上面代码的第7步)，RunLoop 调用这个函数去接收消息，如果没有别人发送 port 消息过来，内核会将线程置于等待状态。例如你在模拟器里跑起一个 iOS 的 App，然后在 App 静止时点击暂停，你会看到主线程调用栈是停留在 `mach_msg_trap()` 这个地方。

关于具体的如何利用 mach port 发送信息，可以看看 [NSHipster 这一篇文章](http://nshipster.com/inter-process-communication/)，或者[这里](http://segmentfault.com/a/1190000002400329)的中文翻译 。

关于Mach的历史可以看看这篇很有趣的文章：[Mac OS X 背后的故事（三）Mach 之父 Avie Tevanian](http://www.programmer.com.cn/8121/)。

## 苹果用 RunLoop 实现的功能

首先我们可以看一下 App 启动后 RunLoop 的状态：

```
CFRunLoop {
    current mode = kCFRunLoopDefaultMode
    common modes = {
        UITrackingRunLoopMode
        kCFRunLoopDefaultMode
    }
 
    common mode items = {
 
        // source0 (manual)
        CFRunLoopSource {order =-1, {
            callout = _UIApplicationHandleEventQueue}}
        CFRunLoopSource {order =-1, {
            callout = PurpleEventSignalCallback }}
        CFRunLoopSource {order = 0, {
            callout = FBSSerialQueueRunLoopSourceHandler}}
 
        // source1 (mach port)
        CFRunLoopSource {order = 0,  {port = 17923}}
        CFRunLoopSource {order = 0,  {port = 12039}}
        CFRunLoopSource {order = 0,  {port = 16647}}
        CFRunLoopSource {order =-1, {
            callout = PurpleEventCallback}}
        CFRunLoopSource {order = 0, {port = 2407,
            callout = _ZL20notify_port_callbackP12__CFMachPortPvlS1_}}
        CFRunLoopSource {order = 0, {port = 1c03,
            callout = __IOHIDEventSystemClientAvailabilityCallback}}
        CFRunLoopSource {order = 0, {port = 1b03,
            callout = __IOHIDEventSystemClientQueueCallback}}
        CFRunLoopSource {order = 1, {port = 1903,
            callout = __IOMIGMachPortPortCallback}}
 
        // Ovserver
        CFRunLoopObserver {order = -2147483647, activities = 0x1, // Entry
            callout = _wrapRunLoopWithAutoreleasePoolHandler}
        CFRunLoopObserver {order = 0, activities = 0x20,          // BeforeWaiting
            callout = _UIGestureRecognizerUpdateObserver}
        CFRunLoopObserver {order = 1999000, activities = 0xa0,    // BeforeWaiting | Exit
            callout = _afterCACommitHandler}
        CFRunLoopObserver {order = 2000000, activities = 0xa0,    // BeforeWaiting | Exit
            callout = _ZN2CA11Transaction17observer_callbackEP19__CFRunLoopObservermPv}
        CFRunLoopObserver {order = 2147483647, activities = 0xa0, // BeforeWaiting | Exit
            callout = _wrapRunLoopWithAutoreleasePoolHandler}
 
        // Timer
        CFRunLoopTimer {firing = No, interval = 3.1536e+09, tolerance = 0,
            next fire date = 453098071 (-4421.76019 @ 96223387169499),
            callout = _ZN2CAL14timer_callbackEP16__CFRunLoopTimerPv (QuartzCore.framework)}
    },
 
    modes ＝ {
        CFRunLoopMode  {
            sources0 =  { /* same as 'common mode items' */ },
            sources1 =  { /* same as 'common mode items' */ },
            observers = { /* same as 'common mode items' */ },
            timers =    { /* same as 'common mode items' */ },
        },
 
        CFRunLoopMode  {
            sources0 =  { /* same as 'common mode items' */ },
            sources1 =  { /* same as 'common mode items' */ },
            observers = { /* same as 'common mode items' */ },
            timers =    { /* same as 'common mode items' */ },
        },
 
        CFRunLoopMode  {
            sources0 = {
                CFRunLoopSource {order = 0, {
                    callout = FBSSerialQueueRunLoopSourceHandler}}
            },
            sources1 = (null),
            observers = {
                CFRunLoopObserver >{activities = 0xa0, order = 2000000,
                    callout = _ZN2CA11Transaction17observer_callbackEP19__CFRunLoopObservermPv}
            )},
            timers = (null),
        },
 
        CFRunLoopMode  {
            sources0 = {
                CFRunLoopSource {order = -1, {
                    callout = PurpleEventSignalCallback}}
            },
            sources1 = {
                CFRunLoopSource {order = -1, {
                    callout = PurpleEventCallback}}
            },
            observers = (null),
            timers = (null),
        },
        
        CFRunLoopMode  {
            sources0 = (null),
            sources1 = (null),
            observers = (null),
            timers = (null),
        }
    }
}
```

可以看到，系统默认注册了5个Mode:

1. kCFRunLoopDefaultMode: App的默认 Mode，通常主线程是在这个 Mode 下运行的。
2. UITrackingRunLoopMode: 界面跟踪 Mode，用于 ScrollView 追踪触摸滑动，保证界面滑动时不受其他 Mode 影响。
3. UIInitializationRunLoopMode: 在刚启动 App 时第进入的第一个 Mode，启动完成后就不再使用。
4. GSEventReceiveRunLoopMode: 接受系统事件的内部 Mode，通常用不到。
5. kCFRunLoopCommonModes: 这是一个占位的 Mode，没有实际作用。

你可以在[这里](http://iphonedevwiki.net/index.php/CFRunLoop)看到更多的苹果内部的 Mode，但那些 Mode 在开发中就很难遇到了。

当 RunLoop 进行回调时，一般都是通过一个很长的函数调用出去 (call out), 当你在你的代码中下断点调试时，通常能在调用栈上看到这些函数。下面是这几个函数的整理版本，如果你在调用栈中看到这些长函数名，在这里查找一下就能定位到具体的调用地点了：

```
{
    /// 1. 通知Observers，即将进入RunLoop
    /// 此处有Observer会创建AutoreleasePool: _objc_autoreleasePoolPush();
    __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopEntry);
    do {
 
        /// 2. 通知 Observers: 即将触发 Timer 回调。
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopBeforeTimers);
        /// 3. 通知 Observers: 即将触发 Source (非基于port的,Source0) 回调。
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopBeforeSources);
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_BLOCK__(block);
 
        /// 4. 触发 Source0 (非基于port的) 回调。
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__(source0);
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_BLOCK__(block);
 
        /// 6. 通知Observers，即将进入休眠
        /// 此处有Observer释放并新建AutoreleasePool: _objc_autoreleasePoolPop(); _objc_autoreleasePoolPush();
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopBeforeWaiting);
 
        /// 7. sleep to wait msg.
        mach_msg() -> mach_msg_trap();
        
 
        /// 8. 通知Observers，线程被唤醒
        __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopAfterWaiting);
 
        /// 9. 如果是被Timer唤醒的，回调Timer
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_TIMER_CALLBACK_FUNCTION__(timer);
 
        /// 9. 如果是被dispatch唤醒的，执行所有调用 dispatch_async 等方法放入main queue 的 block
        __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__(dispatched_block);
 
        /// 9. 如果如果Runloop是被 Source1 (基于port的) 的事件唤醒了，处理这个事件
        __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE1_PERFORM_FUNCTION__(source1);
 
 
    } while (...);
 
    /// 10. 通知Observers，即将退出RunLoop
    /// 此处有Observer释放AutoreleasePool: _objc_autoreleasePoolPop();
    __CFRUNLOOP_IS_CALLING_OUT_TO_AN_OBSERVER_CALLBACK_FUNCTION__(kCFRunLoopExit);
}
```

#### AutoreleasePool

App启动后，苹果在主线程 RunLoop 里注册了两个 Observer，其回调都是 `_wrapRunLoopWithAutoreleasePoolHandler()`。

第一个 Observer 监视的事件是 Entry(即将进入Loop)，其回调内会调用 `_objc_autoreleasePoolPush()` 创建自动释放池。其 order 是-2147483647，优先级最高，保证创建释放池发生在其他所有回调之前。

第二个 Observer 监视了两个事件： BeforeWaiting(准备进入休眠) 时调用`_objc_autoreleasePoolPop()` 和 `_objc_autoreleasePoolPush()` 释放旧的池并创建新池；Exit(即将退出Loop) 时调用 `_objc_autoreleasePoolPop()` 来释放自动释放池。这个 Observer 的 order 是 2147483647（`2^31-1`
），优先级最低，保证其释放池子发生在其他所有回调之后。

在主线程执行的代码，通常是写在诸如事件回调、Timer回调内的。这些回调会被 RunLoop 创建好的 AutoreleasePool 环绕着，所以不会出现内存泄漏，开发者也不必显示创建 Pool 了。

#### 事件响应

苹果注册了一个 Source1 (基于 mach port 的) 用来接收系统事件，其回调函数为 `__IOHIDEventSystemClientQueueCallback()`。

当一个硬件事件(触摸/锁屏/摇晃等)发生后，首先由 `IOKit.framework` 生成一个 IOHIDEvent 事件并由 SpringBoard 接收。这个过程的详细情况可以参考[这里](http://iphonedevwiki.net/index.php/IOHIDFamily)。SpringBoard 只接收按键(锁屏/静音等)，触摸，加速，接近传感器等几种 Event，随后用 mach port 转发给需要的App进程。随后苹果注册的那个 Source1 就会触发回调，并调用 `_UIApplicationHandleEventQueue()` 进行应用内部的分发。

`_UIApplicationHandleEventQueue()` 会把 IOHIDEvent 处理并包装成 UIEvent 进行处理或分发，其中包括识别 UIGesture/处理屏幕旋转/发送给 UIWindow 等。通常事件比如 UIButton 点击、touchesBegin/Move/End/Cancel 事件都是在这个回调中完成的。

#### 手势识别

当上面的 `_UIApplicationHandleEventQueue()` 识别了一个手势时，其首先会调用 Cancel 将当前的 touchesBegin/Move/End 系列回调打断。随后系统将对应的 UIGestureRecognizer 标记为待处理。

苹果注册了一个 Observer 监测 BeforeWaiting (Loop即将进入休眠) 事件，这个Observer的回调函数是 `_UIGestureRecognizerUpdateObserver()`，其内部会获取所有刚被标记为待处理的 GestureRecognizer，并执行GestureRecognizer的回调。

当有 UIGestureRecognizer 的变化(创建/销毁/状态改变)时，这个回调都会进行相应处理。

#### 界面更新

当在操作 UI 时，比如改变了 Frame、更新了 UIView/CALayer 的层次时，或者手动调用了 UIView/CALayer 的 `setNeedsLayout/setNeedsDisplay`方法后，这个 UIView/CALayer 就被标记为待处理，并被提交到一个全局的容器去。

苹果注册了一个 Observer 监听 BeforeWaiting(即将进入休眠) 和 Exit (即将退出Loop) 事件，回调去执行一个很长的函数：
`_ZN2CA11Transaction17observer_callbackEP19__CFRunLoopObservermPv()`。这个函数里会遍历所有待处理的 UIView/CALayer 以执行实际的绘制和调整，并更新 UI 界面。

这个函数内部的调用栈大概是这样的：

```
_ZN2CA11Transaction17observer_callbackEP19__CFRunLoopObservermPv()
    QuartzCore:CA::Transaction::observer_callback:
        CA::Transaction::commit();
            CA::Context::commit_transaction();
                CA::Layer::layout_and_display_if_needed();
                    CA::Layer::layout_if_needed();
                        [CALayer layoutSublayers];
                            [UIView layoutSubviews];
                    CA::Layer::display_if_needed();
                        [CALayer display];
                            [UIView drawRect];
```
#### 定时器

**NSTimer** 其实就是 CFRunLoopTimerRef，他们之间是 toll-free bridged 的。一个 NSTimer 注册到 RunLoop 后，RunLoop 会为其重复的时间点注册好事件。例如 10:00, 10:10, 10:20 这几个时间点。RunLoop为了节省资源，并不会在非常准确的时间点回调这个Timer。Timer 有个属性叫做 Tolerance (宽容度)，标示了当时间点到后，容许有多少最大误差。

如果某个时间点被错过了，例如执行了一个很长的任务，则那个时间点的回调也会跳过去，不会延后执行。就比如等公交，如果 10:10 时我忙着玩手机错过了那个点的公交，那我只能等 10:20 这一趟了。

**CADisplayLink** 是一个和屏幕刷新率一致的定时器（但实际实现原理更复杂，和 NSTimer 并不一样，其内部实际是操作了一个 Source）。如果在两次屏幕刷新之间执行了一个长任务，那其中就会有一帧被跳过去（和 NSTimer 相似），造成界面卡顿的感觉。在快速滑动TableView时，即使一帧的卡顿也会让用户有所察觉。Facebook 开源的 AsyncDisplayLink 就是为了解决界面卡顿的问题，其内部也用到了 RunLoop，这个稍后我会再单独写一页博客来分析。

#### PerformSelecter

当调用 NSObject 的 `performSelecter:afterDelay:` 后，实际上其内部会创建一个 Timer 并添加到当前线程的 RunLoop 中。所以如果当前线程没有 RunLoop，则这个方法会失效。

当调用 `performSelector:onThread:` 时，实际上其会创建一个 Timer 加到对应的线程去，同样的，如果对应线程没有 RunLoop 该方法也会失效。

#### 关于GCD

实际上 RunLoop 底层也会用到 GCD 的东西，~~比如 RunLoop 是用 dispatch_source_t 实现的 Timer~~（评论中有人提醒，NSTimer 是用了 XNU 内核的 mk_timer，我也仔细调试了一下，发现 NSTimer 确实是由 mk_timer 驱动，而非 GCD 驱动的）。但同时 GCD 提供的某些接口也用到了 RunLoop， 例如 dispatch_async()。

当调用 `dispatch_async(dispatch_get_main_queue(), block)` 时，libDispatch 会向主线程的 RunLoop 发送消息，RunLoop会被唤醒，并从消息中取得这个 block，并在回调 `__CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__()` 里执行这个 block。但这个逻辑仅限于 dispatch 到主线程，dispatch 到其他线程仍然是由 libDispatch 处理的。

#### 关于网络请求

iOS 中，关于网络请求的接口自下至上有如下几层:

```
CFSocket
CFNetwork       ->ASIHttpRequest
NSURLConnection ->AFNetworking
NSURLSession    ->AFNetworking2, Alamofire
```

- CFSocket 是最底层的接口，只负责 socket 通信。
- CFNetwork 是基于 CFSocket 等接口的上层封装，ASIHttpRequest 工作于这一层。
- NSURLConnection 是基于 CFNetwork 的更高层的封装，提供面向对象的接口，AFNetworking 工作于这一层。
- NSURLSession 是 iOS7 中新增的接口，表面上是和 NSURLConnection 并列的，但底层仍然用到了 NSURLConnection 的部分功能 (比如 `com.apple.NSURLConnectionLoader` 线程)，AFNetworking2 和 Alamofire 工作于这一层。

下面主要介绍下 NSURLConnection 的工作过程。

通常使用 NSURLConnection 时，你会传入一个 Delegate，当调用了 `[connection start]` 后，这个 Delegate 就会不停收到事件回调。实际上，start 这个函数的内部会会获取 CurrentRunLoop，然后在其中的 DefaultMode 添加了4个 Source0 (即需要手动触发的Source)。CFMultiplexerSource 是负责各种 Delegate 回调的，CFHTTPCookieStorage 是处理各种 Cookie 的。

当开始网络传输时，我们可以看到 NSURLConnection 创建了两个新线程：com.apple.NSURLConnectionLoader 和 com.apple.CFSocket.private。其中 CFSocket 线程是处理底层 socket 连接的。NSURLConnectionLoader 这个线程内部会使用 RunLoop 来接收底层 socket 的事件，并通过之前添加的 Source0 通知到上层的 Delegate。

![](http://ww3.sinaimg.cn/large/7853084cjw1fa7xj3vs8ij20hs0cdt9m.jpg)

NSURLConnectionLoader 中的 RunLoop 通过一些基于 mach port 的 Source 接收来自底层 CFSocket 的通知。当收到通知后，其会在合适的时机向 CFMultiplexerSource 等 Source0 发送通知，同时唤醒 Delegate 线程的 RunLoop 来让其处理这些通知。CFMultiplexerSource 会在 Delegate 线程的 RunLoop 对 Delegate 执行实际的回调。

## RunLoop 的实际应用举例

#### AFNetworking

[AFURLConnectionOperation](https://github.com/AFNetworking/AFNetworking) 这个类是基于 NSURLConnection 构建的，其希望能在后台线程接收 Delegate 回调。为此 AFNetworking 单独创建了一个线程，并在这个线程中启动了一个 RunLoop：

```
+ (void)networkRequestThreadEntryPoint:(id)__unused object {
    @autoreleasepool {
        [[NSThread currentThread] setName:@"AFNetworking"];
        NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
        [runLoop addPort:[NSMachPort port] forMode:NSDefaultRunLoopMode];
        [runLoop run];
    }
}
 
+ (NSThread *)networkRequestThread {
    static NSThread *_networkRequestThread = nil;
    static dispatch_once_t oncePredicate;
    dispatch_once(&oncePredicate, ^{
        _networkRequestThread = [[NSThread alloc] initWithTarget:self selector:@selector(networkRequestThreadEntryPoint:) object:nil];
        [_networkRequestThread start];
    });
    return _networkRequestThread;
}
```

RunLoop 启动前内部必须要有至少一个 Timer/Observer/Source，所以 AFNetworking 在 [runLoop run] 之前先创建了一个新的 NSMachPort 添加进去了。通常情况下，调用者需要持有这个 NSMachPort (mach_port) 并在外部线程通过这个 port 发送消息到 loop 内；但此处添加 port 只是为了让 RunLoop 不至于退出，并没有用于实际的发送消息。

```
- (void)start {
    [self.lock lock];
    if ([self isCancelled]) {
        [self performSelector:@selector(cancelConnection) onThread:[[self class] networkRequestThread] withObject:nil waitUntilDone:NO modes:[self.runLoopModes allObjects]];
    } else if ([self isReady]) {
        self.state = AFOperationExecutingState;
        [self performSelector:@selector(operationDidStart) onThread:[[self class] networkRequestThread] withObject:nil waitUntilDone:NO modes:[self.runLoopModes allObjects]];
    }
    [self.lock unlock];
}
```

当需要这个后台线程执行任务时，AFNetworking 通过调用 [NSObject performSelector:onThread:..] 将这个任务扔到了后台线程的 RunLoop 中。

#### AsyncDisplayKit

[AsyncDisplayKit](https://github.com/facebook/AsyncDisplayKit) 是 Facebook 推出的用于保持界面流畅性的框架，其原理大致如下：

UI 线程中一旦出现繁重的任务就会导致界面卡顿，这类任务通常分为3类：排版，绘制，UI对象操作。

排版通常包括计算视图大小、计算文本高度、重新计算子式图的排版等操作。

绘制一般有文本绘制 (例如 CoreText)、图片绘制 (例如预先解压)、元素绘制 (Quartz)等操作。

UI对象操作通常包括 UIView/CALayer 等 UI 对象的创建、设置属性和销毁。

其中前两类操作可以通过各种方法扔到后台线程执行，而最后一类操作只能在主线程完成，并且有时后面的操作需要依赖前面操作的结果 （例如TextView创建时可能需要提前计算出文本的大小）。ASDK 所做的，就是尽量将能放入后台的任务放入后台，不能的则尽量推迟 (例如视图的创建、属性的调整)。

为此，ASDK 创建了一个名为 ASDisplayNode 的对象，并在内部封装了 UIView/CALayer，它具有和 UIView/CALayer 相似的属性，例如 frame、backgroundColor等。所有这些属性都可以在后台线程更改，开发者可以只通过 Node 来操作其内部的 UIView/CALayer，这样就可以将排版和绘制放入了后台线程。但是无论怎么操作，这些属性总需要在某个时刻同步到主线程的 UIView/CALayer 去。

ASDK 仿照 QuartzCore/UIKit 框架的模式，实现了一套类似的界面更新的机制：即在主线程的 RunLoop 中添加一个 Observer，监听了 kCFRunLoopBeforeWaiting 和 kCFRunLoopExit 事件，在收到回调时，遍历所有之前放入队列的待处理的任务，然后一一执行。
具体的代码可以看这里[_ASAsyncTransactionGroup](https://github.com/facebook/AsyncDisplayKit/blob/master/AsyncDisplayKit%2FDetails%2FTransactions%2F_ASAsyncTransactionGroup.m)。


# see also

>* [《深入理解RunLoop》](http://blog.ibireme.com/2015/05/18/runloop/)

