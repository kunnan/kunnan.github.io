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

#### 3.2  Input Sources

>* Input sources deliver events asynchronously to your threads
>```
>1)  Port-based input sources monitor your application’s Mach ports.
>2)  Custom input sources monitor custom sources of events. 
>```

>*  The only difference between the two sources is how they are signaled.
>```
> 1)Port-based sources are signaled automatically by the kernel, 
> 2) and custom sources must be signaled manually from another thread
>```


###### 3.2.1 CFRunLoopSourceRef---perform

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

###### 3.2.2 Port-Based Sources: Source1

>* For example, in Cocoa, you never have to create an input source directly at all. 
>```
>You simply create a port object and use the methods of NSPort to add that port to the run loop. 
>The port object handles the creation and configuration of the needed input source for you.
```
>* In `Core Foundation`, you must manually create both the port and its run loop source.
>
>
>* In both cases, you use the functions associated with the port opaque type ([`CFMachPortRef`](https://developer.apple.com/documentation/corefoundation/cfmachport), [`CFMessagePortRef`](https://developer.apple.com/documentation/corefoundation/cfmessageportref), or [`CFSocketRef`](https://developer.apple.com/documentation/corefoundation/cfsocket)) to create the appropriate objects
>* [ Configuring a Port-Based Input Source.](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-131281)


###### 3.2.3 Custom Input Sources: Source0

>* To create a custom input source, you must use the functions associated with the `CFRunLoopSourceRef` opaque type in Core Foundation. 
>```
>You configure a custom input source using several callback functions.
> Core Foundation calls these functions at different points to configure the source, handle any incoming events, and tear down the source when it is removed from the run loop.
>```
>* [For an example of how to create a custom input source](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW3)
>
>* [see also CFRunLoopSource Reference](https://developer.apple.com/documentation/corefoundation/cfrunloopsource-rhr)

>* [Source0的例子：运行runLoop 一次，阻塞当前线程以等待处理一次输入源](https://gist.github.com/zhangkn/2721a43436676e15c8f8be497dac9901)
><script src="https://gist.github.com/zhangkn/2721a43436676e15c8f8be497dac9901.js"></script>

>* [CFUserNotificationCreateRunLoopSource](https://developer.apple.com/documentation/corefoundation/1534507-cfusernotificationcreaterunloops?language=objc)
>


###### 3.2.4 Cocoa Perform Selector Sources

>* Cocoa defines a custom input source that allows you to perform a selector on any thread
>```
>a perform selector source removes itself from the run loop after it performs its selector.
>```

>*  Performing selectors on other threads
><script src="https://gist.github.com/zhangkn/8713914c94be503613711b1f346c3693.js"></script>
>

#### 3.3 CFRunLoopTimerRef: Timer Sources

>* Timer sources deliver events synchronously to your threads at a preset time in the future. Timers are a way for a thread to notify itself to do something
>```
>Like input sources, timers are associated with specific modes of your run loop.
>You can configure timers to generate events only once or repeatedly
>```


>* **CFRunLoopTimerRef** 是基于时间的触发器，它和 NSTimer 是toll-free bridged 的，可以混用。
>```其包含一个时间长度和一个回调（函数指针）。
>当其加入到 RunLoop 时，RunLoop会注册对应的时间点，当时间点到时，RunLoop会被唤醒以执行那个回调。
>```
><script src="https://gist.github.com/zhangkn/dc035d074cc80a31d8417e7be11bc5d8.js"></script>

>* [see Configuring Timer Sources](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW6)
>* [ see NSTimer Class Reference ](https://developer.apple.com/documentation/foundation/timer)
>* [ CFRunLoopTimer Reference](https://developer.apple.com/documentation/corefoundation/cfrunlooptimer-rhk)
>

#### 3.4 CFRunLoopObserverRef: Run Loop Observers


>*  To create a run loop observer, you create a new instance of the CFRunLoopObserverRef opaque type. This type keeps track of your custom callback function and the activities in which it is interested

>* **CFRunLoopObserverRef** 是观察者，每个 Observer 都包含了一个回调（函数指针）
><script src="https://gist.github.com/zhangkn/2d32f6bbe5a09f338355e0049334132c.js"></script>
>
>* You might use run loop observers to prepare your thread to process a given event or to prepare the thread before it goes to sleep. You can associate run loop observers with the following events in your run loop:
><script src="https://gist.github.com/zhangkn/1a990ad34ea7c2dd26d055f8afbdb964.js"></script>

>* [see Configuring the Run Loop](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW18)
>* [CFRunLoopObserver Reference](https://developer.apple.com/documentation/corefoundation/cfrunloopobserver)


#### 3.5 mode item

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


A run loop mode is a collection of input sources and timers to be monitored and a collection of run loop observers to be notified.

>* In your code, you identify modes by name.
>```
>You can define custom modes by simply specifying a custom string for the mode name.
> You must be sure to add one or more input sources, timers, or run-loop observers to any modes you create for them to be useful.
>```
>
>* Note: Modes discriminate based on the source of the event
>```
> You could use modes to listen to a different set of ports, suspend timers temporarily, or otherwise change the sources and run loop observers currently being monitored
>```


>* [swift-corelibs-foundation/CoreFoundation/RunLoop.subproj/CFRunLoop.c](https://github.com/apple/swift-corelibs-foundation/blob/386f3a5d71dc51845abddbf8c2ffb3179cd5cc89/CoreFoundation/RunLoop.subproj/CFRunLoop.c)
>* CFRunLoopMode结构
><script src="https://gist.github.com/zhangkn/acecc505d64fd8741072e0cee6195f23.js"></script>
>* CFRunLoop 的结构大致如下：
><script src="https://gist.github.com/zhangkn/6d567a95877cac9839f029c9342bf148.js"></script>


>* 重要概念`CommonModes`：
>```
>NSRunLoopCommonModes (Cocoa)
kCFRunLoopCommonModes (Core Foundation)
This is a configurable group of commonly used modes. Associating an input source with this mode also associates it with each of the modes in the group. For Cocoa applications, this set includes the default, modal, and event tracking modes by default. Core Foundation includes just the default mode initially. You can add custom modes to the set using the CFRunLoopAddCommonMode function.
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
>

###### 4.2.1 Predefined run loop modes

>* Default
>```
NSDefaultRunLoopMode (Cocoa)
kCFRunLoopDefaultMode (Core Foundation)
The default mode is the one used for most operations. Most of the time, you should use this mode to start your run loop and configure your input sources.
>```
>* Connection
>```
>NSConnectionReplyMode (Cocoa)
>Cocoa uses this mode in conjunction with NSConnection objects to monitor replies. You should rarely need to use this mode yourself.
>```
>* Modal
>```
>NSModalPanelRunLoopMode (Cocoa)
>Cocoa uses this mode to identify events intended for modal panels.
>```
>* Event tracking
>```
>NSEventTrackingRunLoopMode (Cocoa)
>Cocoa uses this mode to restrict incoming events during mouse-dragging loops and other sorts of user interface tracking loops.
>```
>* Common modes
>```
> NSRunLoopCommonModes (Cocoa)
kCFRunLoopCommonModes (Core Foundation)
>```


#### 4.3 同时苹果还提供了一个操作 Common 标记的字符串：

>* `kCFRunLoopCommonModes (NSRunLoopCommonModes)`，你可以用这个字符串来操作 Common Items，或标记一个 Mode 为 "Common"。使用时注意区分这个字符串和其他 mode name。
>


# V、RunLoop的内部逻辑:The Run Loop Sequence of Events


>* Run loop management is not entirely automatic.
>``` You must still design your thread’s code to start the run loop at appropriate times and respond to incoming events
```

#### 5.1 Anatomy of a Run Loop

It is a loop your thread enters and uses to run event handlers in response to incoming events. 

###### 5.1.0 以runUntilDate: 为例子

>* runUntilDate
>
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

>* your thread’s run loop processes pending events and generates notifications for any attached observers
><blockquote><section><ol class="ol"><li class="li"><p>Notify observers that the run loop has been entered.</p></li><li class="li"><p>Notify observers that any ready timers are about to fire.</p></li><li class="li"><p>Notify observers that any input sources that are not port based are about to fire.</p></li><li class="li"><p>Fire any non-port-based input sources that are ready to fire.</p></li><li class="li"><p>If a port-based input source is ready and waiting to fire, process the event immediately. Go to step 9. </p></li><li class="li"><p>Notify observers that the thread is about to sleep.</p></li><li class="li"><p>Put the thread to sleep until one of the following events occurs:</p><ul class="ul"><li class="li"><p>An event arrives for a port-based input source.</p></li><li class="li"><p>A timer fires.</p></li><li class="li"><p>The timeout value set for the run loop expires.</p></li><li class="li"><p>The run loop is explicitly woken up. </p></li></ul></li><li class="li"><p>Notify observers that the thread just woke up.</p></li><li class="li"><p>Process the pending event.</p><ul class="ul"><li class="li"><p>If a user-defined timer fired, process the timer event and restart the loop. Go to step 2.</p></li><li class="li"><p>If an input source fired, deliver the event.</p></li><li class="li"><p>If the run loop was explicitly woken up but has not yet timed out, restart the loop. Go to step 2.</p></li></ul></li><li class="li"><p>Notify observers that the run loop has exited.</p></li></section></blockquote>



# VI、 When Would You Use a Run Loop?

>* For example, you need to start a run loop if you plan to do any of the following
><script src="https://gist.github.com/zhangkn/25a0325a70a7696536cd03d3cf584f34.js"></script>

>* Information on how to configure and exit a run loop is described in [`Using Run Loop Objects`](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW5)
>
>
# VII、Using Run Loop Objects

>* A run loop object provides the main interface for adding input sources, timers, and run-loop observers to your run loop and then running it.
>```
> Every thread has a single run loop object associated with it. 
> In Cocoa, this object is an instance of the NSRunLoop class. 
> In a low-level application, it is a pointer to a CFRunLoopRef opaque type.
> ```
> 

#### 7.1 Getting a Run Loop Object
>* In a Cocoa application, use the `currentRunLoop `class method of` NSRunLoop` to retrieve an NSRunLoop object.
>* Use the CFRunLoopGetCurrent function.


>* Although they are not toll-free bridged types, you can get a CFRunLoopRef opaque type from an NSRunLoop object when needed.
>```
> The NSRunLoop class defines a getCFRunLoop method that returns a CFRunLoopRef type that you can pass to Core Foundation routines. 
> Because both objects refer to the same run loop, you can intermix calls to the NSRunLoop object and CFRunLoopRef opaque type as needed.
> ```

#### 7.2 Configuring the Run Loop

>* Before you run a run loop on a secondary thread, you must add at least one input source or timer to it.

>* [see Configuring Run Loop Sources](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW7)
>*  Creating a run loop observer
><script src="https://gist.github.com/zhangkn/98d7c32cec7f85b27a809d2cfebc7abe.js"></script>
>

#### 7.3 Starting the Run Loop


A run loop must have at least one input source or timer to monitor. If one is not attached, the run loop exits immediately.

>* There are several ways to start the run loop, including the following:
>```
>1、Unconditionally
>2、With a set time limit
>3、In a particular mode
>```


>* unconditionally
>```
>You can add and remove input sources and timers, but the only way to stop the run loop is to kill it
>```

>* it is better to run the run loop with a timeout value
>```
>When you use a timeout value, the run loop runs until an event arrives or the allotted time expires.
> 1) If an event arrives, that event is dispatched to a handler for processing and then the run loop exits. Your code can then restart the run loop to handle the next event.
> 2)If the allotted time expires instead, you can simply restart the run loop or use the time to do any needed housekeeping
>```
>* you can also run your run loop using a specific mod
>```
> more detail in Run Loop Modes: https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW12
>```

>* example: Running a run loop 
><script src="https://gist.github.com/zhangkn/add4bd6a5ce53afff8f036405eaeb11f.js"></script>
>

#### 7.4 Exiting the Run Loop

>* There are two ways to make a run loop exit before it has processed an event:
>```
>1) Configure the run loop to run with a timeout value.
2) Tell the run loop to stop.
>```

>* Using a timeout value is certainly preferred, if you can manage it
>```
> Specifying a timeout value lets the run loop finish all of its normal processing, including delivering notifications to run loop observers, before exiting.
>```
>* topping the run loop explicitly with the `CFRunLoopStop `function produces a result similar to a timeout.
>```
> The run loop sends out any remaining run-loop notifications and then exits
>```
>* Although removing a run loop’s `input sources `and `timers `may also cause the run loop to exit, this is not a reliable way to stop a run loop



#### 7.5 Thread Safety and Run Loop Objects

>* The functions in Core Foundation are generally thread-safe and can be called from any thread
 
>* The Cocoa NSRunLoop class is not as inherently thread safe as its Core Foundation counterpart
>```
>Adding an input source or timer to a run loop belonging to a different thread could cause your code to crash or behave in an unexpected way.
>```
 
# VIII、 Configuring Run Loop Sources

#### 8.1 Defining a Custom Input Source

>* Creating a custom input source involves defining the following:
>```
>1)The information you want your input source to process.
>2) A scheduler routine to let interested clients know how to contact your input source.
>3) A handler routine to perform requests sent by any clients.
>4) A cancellation routine to invalidate your input source.
>```


>* Operating a custom input source
>![](https://ws1.sinaimg.cn/large/006tKfTcgy1fr1hdj8payj30cx069wem.jpg)
>

#### 8.2 Defining the Input Source

>* uses an Objective-C object to manage a command buffer and coordinate with the run loop.
><script src="https://gist.github.com/zhangkn/44ba7e91139a88e67a94fd4306a60747.js"></script>
>* Scheduling a run loop source
><script src="https://gist.github.com/zhangkn/f9a5b5006cf6cbf4849120907a7c3f0d.js"></script>
>
>*   Performing work in the input source
><script src="https://gist.github.com/zhangkn/d9ab4be615fc54d6b9fdd1e6e056efa2.js"></script>
>*  Invalidating an input source
><script src="https://gist.github.com/zhangkn/aaf02ae9a5190fe1bc3847f86d24bc5e.js"></script>
>

#### 8.3 Installing the Input Source on the Run Loop


>* Installing the run loop source
><script src="https://gist.github.com/zhangkn/e4f46747de954936f78ad087556c2390.js"></script>
>

#### 8.4 Coordinating with Clients of the Input Source

>*  Registering and removing an input source with the application delegate
><script src="https://gist.github.com/zhangkn/b241a913f2affaa065a48c8e4880115c.js"></script>
>

#### 8.5  Signaling the Input Source


After it hands off its data to the input source, a client must signal the source and wake up its run loop.

>* shows the fireCommandsOnRunLoop method of the RunLoopSource object
><script src="https://gist.github.com/zhangkn/f5deca4778769437ee6854ba012bf001.js"></script>
>

# IX Configuring Timer Sources

>* create a timer object and schedule it on your run loop
 ```
1)  In Cocoa, you use the NSTimer class to create new timer objects,
2) and in Core Foundation you use the CFRunLoopTimerRef opaque type. 
 ```
 
>* In Cocoa, you can create and schedule a timer all at once using either of these class methods:
>```
> scheduledTimerWithTimeInterval:target:selector:userInfo:repeats:
scheduledTimerWithTimeInterval:invocation:repeats:
These methods create the timer and add it to the current thread’s run loop in the default mode (NSDefaultRunLoopMode).
>```
>* adding it to the run loop using the addTimer:forMode: method of NSRunLoop

#### 9.1  how to create timers using both techniques: `NSTimer`、 `Core Foundation`

###### 9.1.1  Creating and scheduling timers using NSTimer

<script src="https://gist.github.com/zhangkn/a02696658453e0e42504c549821399c5.js"></script>


###### 9.1.2 Creating and scheduling a timer using Core Foundation


>* [see its description in CFRunLoopTimer Reference.](https://developer.apple.com/documentation/corefoundation/cfrunlooptimer-rhk)
><script src="https://gist.github.com/zhangkn/57b17fdec91b0ea50a6d2f95e8c288c4.js"></script>
>

# X、Configuring a Port-Based Input Source

Both Cocoa and Core Foundation provide port-based objects for communicating between threads or between processes. 

#### 10.0 Configuring an NSMachPort Object


>* Main thread launch method
><script src="https://gist.github.com/zhangkn/ed82aff56f402a276584e7e63a8b98fd.js"></script>
>*  Handling Mach port messages
><script src="https://gist.github.com/zhangkn/ba665ef4e3e6dc8bc07f5165410a0282.js"></script>
>*  Launching the worker thread using Mach ports
><script src="https://gist.github.com/zhangkn/d55256fa9371e9696d236b08ddac2e42.js"></script>
>* Sending the check-in message using Mach ports
><script src="https://gist.github.com/zhangkn/13a0f61387b9328952c2ed498931223a.js"></script>
>

#### 10.1 Configuring an NSMessagePort Object

>*  Registering a message port
><script src="https://gist.github.com/zhangkn/395cacd73ce163b59c1ff784843809d2.js"></script>
>
>

#### 10.2 Configuring a Port-Based Input Source in Core Foundation

>*   Attaching a Core Foundation message port to a new thread
><script src="https://gist.github.com/zhangkn/300df7400f6c4c5c9f610d136da77f0c.js"></script>
>*   Receiving the checkin message
><script src="https://gist.github.com/zhangkn/18ad17b2686f0b11ca5ab529168f0e7d.js"></script>
>* Setting up the thread structures
><script src="https://gist.github.com/zhangkn/15d1174543bf45d32c52fcd74bbdb584.js"></script>
>

# XI、RunLoop 的底层实现

 RunLoop 的核心是基于 mach port 的，其进入休眠时调用的函数是 `mach_msg()`。为了解释这个逻辑，下面稍微介绍一下 OSX/iOS 的系统架构。

![](http://ww4.sinaimg.cn/large/7853084cjw1fa7xzae9dlj206203nwel.jpg)



#### 11.1 苹果官方将整个系统大致划分为上述4个层次：

>* 应用层包括用户能接触到的图形应用: Spotlight、Aqua、SpringBoard 
>* 应用框架层即开发人员接触到的 Cocoa 等框架。
>* 核心框架层包括各种核心框架、OpenGL 等内容。
>* Darwin 即操作系统的核心，包括系统内核、驱动、Shell 等内容，这一层是开源的，其所有源码在 [opensource.apple.com](https://opensource.apple.com/) 

#### 11.2 深入看一下 Darwin 这个核心的架构：

![](http://ww4.sinaimg.cn/large/7853084cjw1fa7xzt0n5wj2070060wel.jpg)

>* 在硬件层上面的三个组成部分：
>```
>Mach、BSD、IOKit (还包括一些上面没标注的内容)，共同组成了 XNU 内核。
>```

>* [XNU 内核的内环被称作 Mach，](https://zhangkn.github.io/2018/01/Inter-processCommunicationByRrocketbootstrap/)
>```
>其作为一个微内核，仅提供了诸如处理器调度、IPC (进程间通信)等非常少量的基础服务。
>1) 在 Mach 中，所有的东西都是通过自己的对象实现的，进程、线程和虚拟内存都被称为"对象"。
>2) Mach 的对象间不能直接调用，只能通过消息传递的方式实现对象间的通信。
>3) "消息"是 Mach 中最基础的概念，消息在两个端口 (port) 之间传递，这就是 Mach 的 `IPC (进程间通信)` 的核心。
>```

>* BSD 层可以看作围绕 Mach 层的一个外环，
>```
>其提供了诸如进程管理、文件系统和网络等功能。
>```

>* IOKit 层是为设备驱动提供了一个面向对象(C++)的一个框架。
>

>* Mach 的消息定义是在 `<mach/message.h>` 头文件的，很简单：
><script src="https://gist.github.com/zhangkn/50c15e92a243fcee8a38f16b38621cd6.js"></script>
>![](https://ws1.sinaimg.cn/large/006tKfTcgy1fr1kzsllsuj30lu0f2mxi.jpg)

>* [System_call](http://en.wikipedia.org/wiki/System_call)、[Trap_(computing)](http://en.wikipedia.org/wiki/Trap_(computing))。
>


#### 11.3  `mach_msg()`

>* RunLoop 的核心就是一个`mach_msg()`  
>```
RunLoop 调用这个函数去接收消息，如果没有别人发送 port 消息过来，内核会将线程置于等待状态。
1)例如你在模拟器里跑起一个 iOS 的 App，然后在 App 静止时点击暂停，你会看到主线程调用栈是停留在 `mach_msg_trap()` 这个地方。
```


>* [NSHipster:如何利用 mach port 发送信息](http://nshipster.com/inter-process-communication/)，或者[这里](http://segmentfault.com/a/1190000002400329)的中文翻译 。

>* [Mac OS X 背后的故事（三）Mach 之父 Avie Tevanian:Mach的历史](http://www.programmer.com.cn/8121/)。


# XII、苹果用 RunLoop 实现的功能


>* App 启动后 RunLoop 的状态：
><script src="https://gist.github.com/zhangkn/44e2d4c7e6f447abff5178793f70e8d2.js"></script>

>* [苹果内部的 Mode](http://iphonedevwiki.net/index.php/CFRunLoop)
>

>当 RunLoop 进行回调时，一般都是通过一个很长的函数调用出去 (call out), 当你在你的代码中下断点调试时，通常能在调用栈上看到这些函数：
><script src="https://gist.github.com/zhangkn/877472f7ec948cda7ecffd8ade52896b.js"></script>


#### 12.1  AutoreleasePool


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

