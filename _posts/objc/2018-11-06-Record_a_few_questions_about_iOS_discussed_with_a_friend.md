---
layout: post
title: Record_a_few_questions_about_iOS_discussed_with_a_friend
date: 2018-11-06
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 记录和一个朋友讨论的关于iOS的几个问题:新增weak修饰的object属性的实现方式、UI 事件处理的NSRunLoopMode、和定时器的NSRunLoopMode 的关系是什么样的时候，可以保证它们能并发执行不影响个自的运行
---



# 前言

*  **I、新增weak修饰的object属性的实现方式**
   * 答： 利用weak的实现原理进行实现：先利用OBJC_ASSOCIATION_ASSIGN 进行修饰object；此时需要捕获对象object释放， 利用object 的强引用对象属性OriginalObject的dealloc方法捕获释放的时机，对OBJC_ASSOCIATION_ASSIGN的属性置为空。这样来保证ASSIGN不指向已经被释放的内存地址，达到weak的效果。
*  **[ II、 消息转发的步骤？方法转发：崩溃之前会预留几个步骤](https://segmentfault.com/a/1190000012362645#articleHeader7)**
     * 答：
         *  第一步：resolveInstanceMethod、resolveClassMethod（检查是否动态向该类添加了方法,即运行时动态添加方法）
         *  第二步：forwardingTargetForSelector（该方法返回值对象非nil或非self，则向该返回对象重新发送消息）
         *  第三步：forwardInvocation（ runtime发送methodSignatureForSelector:消息获取Selector对应的方法签名。返回值非空则通过forwardInvocation:转发消息，返回值为空则向当前对象发送doesNotRecognizeSelector:消息，程序崩溃退出）
         *  ![image](https://ws3.sinaimg.cn/large/af39b376gy1fwyj52vs2cj20st07bt9m.jpg)
*  **III、UI滑动的时候出现卡顿的原因（ 排除手势事件冲突之外还有什么原因）？** ---Texture (将UI操作相关的任务转移出主线程：对象的创建、调整、销毁；文本的渲染；图片的解码，图形的绘制；文本宽高的计算，视图布局的计算)
    *  答：当用户频繁滑动ListView时，会在瞬间产生很多个加载数据任务导致线程池的拥堵并随即带来大量的UI更新操作，这些UI操作运行在主线程，造成了一定程度的卡顿。
      *  解决的方法：只要让ListView在滑动的过程中加载数据停止更新UI，在滑动停止后再继续获取数据和更新UI。`UITableViewDataSource 的数据源方法进行处理BOOL canLoad = !self.tableView.dragging && !_tableView.decelerating;`
*  **IV、UI 事件处理的NSRunLoopMode、和定时器的NSRunLoopMode 的关系是什么样的时候，可以保证它们能并发执行不影响个自的运行？**
     * 答：` 事件源，都是处于特定的模式下的，如果和当前runloop的模式不一致则不会得到响应；`
       ![image](https://ws3.sinaimg.cn/large/af39b376gy1fwyhr4pop1j20fk04w75e.jpg)
*  **V、调用控制器vc的 vc.view 的时候，会触发控制器vc的哪个生命周期方法？**
    * 答：viewDidLoad

# I、新增weak修饰的object属性的实现方式？

先利用OBJC_ASSOCIATION_ASSIGN 进行修饰object；此时需要捕获对象object释放， 利用object 的强引用对象属性OriginalObject的dealloc方法捕获释放的时机，对OBJC_ASSOCIATION_ASSIGN的属性置为空。----利用weak的实现原理进行实现。

#### see also
* [runtime/objc-weak.h](https://opensource.apple.com/source/objc4/objc4-646/runtime/objc-weak.h)
* [类别（Category）添加weak （property）属性，关联（Associated）](https://www.jianshu.com/p/18d8cd4ff6c6)
  ![image](https://ws3.sinaimg.cn/large/af39b376gy1fwycw1s76bj20jk082tad.jpg)
   * // 定义一个对象，使用block来回调析构函数。

```
// 定义一个对象，使用block来回调析构函数。
typedef void (^DeallocBlock)();
@interface OriginalObject : NSObject
@property (nonatomic, copy) DeallocBlock block;
- (instancetype)initWithBlock:(DeallocBlock)block;
@end

@implementation OriginalObject

- (instancetype)initWithBlock:(DeallocBlock)block
{
    self = [super init];
    if (self) {
        self.block = block;
    }
    return self;
}
- (void)dealloc {
    self.block ? self.block() : nil;
}
@end
```


   * Category添加属性

```
   // NSObject+property.h
@interface NSObject (property)
@property (nonatomic, weak) id objc_weak_id;
@end

// NSObject+property.m
@implementation NSObject (property)
- (id)objc_weak_id {
    return objc_getAssociatedObject(self, _cmd);
}

- (void)setObjc_weak_id:(id)objc_weak_id {
    OriginalObject *ob = [[OriginalObject alloc] initWithBlock:^{
        objc_setAssociatedObject(self, @selector(objc_weak_id), nil, OBJC_ASSOCIATION_ASSIGN);
    }];
    // 这里关联的key必须唯一，如果使用_cmd，对一个对象多次关联的时候，前面的对象关联会失效。
// 给需要被 assign 修饰的对象添加一个 strong 对象.
    objc_setAssociatedObject(objc_weak_id, (__bridge const void *)(ob.block), ob, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    objc_setAssociatedObject(self, @selector(objc_weak_id), objc_weak_id, OBJC_ASSOCIATION_ASSIGN);
}
```

* 测试代码

```objc
@property (nonatomic,weak) id      weakPoint;
@property (nonatomic,assign) id    assignPoint;// 指针指向的对象已经被释放的话会crash ，通常assign 修饰的是数值对象。因此需要再引用的对象释放的时候，对assignPoint 进行置为nil,避免指向已经释放的内存指针。所以使用assign 来关联属性的时候，想达到weak的效果时，需要捕获对象的销毁时机对assign属性处理。
@property (nonatomic,strong) id    strongPoint;

self.strongPoint = [NSDate date];
self.objc_weak_id = self.strongPoint;
self.weakPoint = self.strongPoint;
NSLog(@"%p", self.weakPoint); // print 指针。
NSLog(@"%p", self.objc_weak_id); // print 相同的指针。
self.strongPoint = nil;

NSLog(@"%p", self.weakPoint); // print 0x0 指针置为空。
NSLog(@"%p", self.objc_weak_id); // print 0x0 指针置为空。
```

  


* [Objective-C 运行时的简单介绍](https://segmentfault.com/a/1190000012362645)


# II、* UI 事件处理的NSRunLoopMode、和定时器的NSRunLoopMode 的关系是什么样的时候，可以保证它们能并发执行不影响个自的运行？

#### RunLoop（“消息”循环）
An event-processing loop, during which events are received and dispatched to appropriate handlers.



#### RunLoop消息类型（事件源）

* input sources （Selector Sources、Port、Customer） 是异步的
* timer sources 是同步的

![image](https://ws3.sinaimg.cn/large/af39b376gy1fwygdhvttaj20rs0ehn1s.jpg)

* Port：内核通过port这种方式将信息发送，而mach则监听内核发来的port信息，然后将其整理，打包发给runloop。
* Customer：开发人员自己发送
* Selector Sources：NSObject类提供了很多方法供我们使用添加到runloop
* Timer Sources：它的事件发送是同步的
* observe不属于事件源，它只是监听runloop本身的状态，并不会影响runloop的生命周期

#### runloop模式

`事件源，都是处于特定的模式下的，如果和当前runloop的模式不一致则不会得到响应；`如果定时器（timer sources）处于mode1，而runloop运行在mode2，则定时器不会触发，只有runloop运行在mode1时，定时器才会触发。因为同一时间只有一个model在运行，因此上述问题的解决方案是将定时器的model类型设置成界面跟踪（input sources） Mode，即NSRunLoopCommonModes。

#### runloop模式的切换：

* 对于非主线程，我们可以退出当前模式，然后再进入另一个模式，也可以直接进入另一个模式，即嵌套
* 对于主线程，我们当然也可以像上面一样操作，但是主线程有其特殊性，有很多系统的事件。系统会做一些切换，我们更关心的是系统是如何切换的？系统切换模式时，并没有使用嵌套



系统为我们提供了多种模式

* kCFRunLoopDefaultMode: App的默认 Mode，通常主线程是在这个 Mode 下运行的。
* UITrackingRunLoopMode: 界面跟踪 Mode，用于 ScrollView 追踪触摸滑动，保证界面滑动时不受其他 Mode 影响。
* UIInitializationRunLoopMode: 在刚启动 App 时第进入的第一个 Mode，启动完成后就不再使用。
* NSRunLoopCommonModes: 包含了多种模式：kCFRunLoopDefaultMode 和UITrackingRunLoopMode。

#### thread--runloop--mode--event sources，关系

![image](https://ws3.sinaimg.cn/large/af39b376gy1fwygnsb7fyj20rs0budmr.jpg)


#### RunLoop生命周期


![image](https://ws3.sinaimg.cn/large/af39b376gy1fwyhaz9gugj20jg0f0tey.jpg)
#### see also

* [ CFRunLoop的源码:](http://opensource.apple.com/tarballs/CF/CF-855.17.tar.gz)


# see also 

* [简历模板](https://github.com/bestswifter/MySampleCode/blob/master/BATInterview/%E5%BC%A0%E6%98%9F%E5%AE%87-%E4%B8%9C%E5%8C%97%E5%A4%A7%E5%AD%A6-iOS%E5%BC%80%E5%8F%91.pdf)
* https://nianxi.net/
* https://blog.csdn.net/yiyaaixuexi/article/category/1429496
* [消息转发](https://nianxi.net/ios/objc-multi-inheritance.html)

>* newpost 
>
```
/Users/devzkn/bin//newpost Record_a_few_questions_about_iOS_discussed_with_a_friend 记录和一个朋友讨论的关于iOS的几个问题 -t objc
> #原来""的参数，需要自己加上""
```

