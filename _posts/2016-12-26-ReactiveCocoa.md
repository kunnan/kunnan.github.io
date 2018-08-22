---
layout:     post
title:      ReactiveCocoa
subtitle:   函数式编程框架ReactiveCocoa
date:       2016-12-26
author:     
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - ReactiveCocoa
    - Open_Source_Framework
---

# 前言

>* 对于一个应用来说，绝大部分的时间都是在等待某些事件的发生或响应某些状态的变化，
>```
>比如用户的触摸事件、应用进入后台、网络请求成功刷新界面等等，而维护这些状态的变化，常常会使代码变得非常复杂，难以扩展。
>```


####  iOS 中的 MVVM 可以分为以下三种不同的实践程度，它们分别对应不同的适用场景

>* MVVM + KVO
```
MVVM + KVO ，适用于现有的 MVC 项目，想转换成 MVVM 但是不打算引入 RAC 作为 binder 的团队；
```
>* MVVM + RAC 
```
MVVM + RAC ，适用于现有的 MVC 项目，想转换成 MVVM 并且打算引入 RAC 作为 binder 的团队；
```
>* MVVM + RAC + ViewModel-Based Navigation
```
MVVM + RAC + ViewModel-Based Navigation ，适用于全新的项目，想实践 MVVM 并且打算引入 RAC 作为 binder ，然后也想实践 ViewModel-Based Navigation 的团队。
```


# [ReactiveCocoa基础](https://github.com/ReactiveCocoa/ReactiveCocoa)


>* Reactive Cocoa就是一个响应式编程的经典框架！ 
![ReactiveCocoa思维导图](http://ww2.sinaimg.cn/large/006y8lVagw1fb7g0gukk8j30m90rl78j.jpg)


#### MVVM数据绑定的实现方式（KVO、block、Delegate、Notification、RAC）

MVVM 的实现可以采用KVO进行数据绑定，也可以采用RAC。— 其实还可以采用block、代理（protocol）实现。


>* block 与protocol 相比的优点
><script src="https://gist.github.com/zhangkn/2bcc599d4218954c5ee71391d50a1473.js"></script>
>
>* KVO 与protocol、block相比的优点
>```
>低耦合通信除了block、protocol之外，还有KVO。
>1)实现的方式中，KVO，不需要通过通知中心就可以进行属性值的监听；
>2)与bock以及代理相比，可以减少代理大量的方法和block大量的处理逻辑代码。
>```
>* RAC 提供了优雅安全的数据绑定。
>```
>1) 优雅集中体现在函数式编程。 
>2) 安全体现在响应式编程：使用RAC解决问题，就不需要考虑调用顺序。每一次操作都写成一系列嵌套的方法中，使代码高聚合，方便管理。
>```


# ReactiveCocoa简介

ReactiveCocoa ，就是用信号接管了iOS 中的所有事件；也就意味着，用一种统一的方式来处理iOS中的所有事件。

####  RAC的用途


>* ReactiveCocoa为事件提供了很多处理方法 
>```
> 比如按钮的点击使用action，ScrollView滚动使用delegate，属性值改变使用KVO等系统提供的方式来处理事件响应。其实这些事件，都可以通过RAC处理
>```
> ![](https://ws2.sinaimg.cn/large/006tKfTcgy1fr3xqa03rwj30ia06j3zl.jpg)


>* 在 iOS 的 MVVM 实现中，我们可以使用 RAC 来在 view 和 viewModel 之间充当 binder 的角色，优雅地实现两者之间的同步。

>* 把 RAC 用在 model 层，使用 Signal 来代表异步的数据获取操作，比如读取文件、访问数据库和网络请求等。
>

#### RAC 体现的编程思想

>* RAC是函数响应式编程（FRP）框架。ReactiveCocoa结合了几种编程风格：函数式编程（Functional Programming）响应式编程（Reactive Programming）

>* ReactiveCocoa（简称为RAC）,符合`高聚合，低耦合`的思想
>```
>ReactiveCocoa为事件提供了很多处理方法，而且利用RAC处理事件很方便，可以把要处理的事情，和监听的事情的代码放在一起(类似block)，这样非常方便我们管理，就不需要跳到对应的方法里。
>```
```

#### RAC 的特点


<script src="https://gist.github.com/zhangkn/632774cb197b375c3a060721fe851bc7.js"></script>


# ReactiveCocoa编程思想


#### 常见的编程思想

>* 1、面向过程：处理事情以过程为核心，一步一步的实现。 
>
>* 2、面向对象：万物皆对象 、万物皆是流
>```
>封装、继承、多态 
1) 封装是为了更好的重用性、可扩展性，但要综合考虑性能问题，即使新增了判断也会增加性能，只是不会量级的增加。 
比如简单工厂和抽象工厂（反射机制）的封装，会增加性能的消耗。但把对象的管理变成了可配置化。 
>```


#### 响应式编程思想

**响应式编程思想**：不需要考虑调用顺序，只需要知道考虑结果，类似于蝴蝶效应，产生一个事件，会影响很多东西，这些事件像流一样的传播出去，然后影响结果。`代表`：KVO、ReactiveCocoa框架

>* 例子
>```
>在程序开发中：
Z ＝ K ＋ N
赋值之后 K  或者 N  的值变化后，Z  的值不会跟着变化
响应式编程，目标就是，如果 K  或者 N  的数值发生变化，Z  的数值会同时发生变化；
>```

#### 链式编程思想

>* **链式编程** 
><script src="https://gist.github.com/zhangkn/cb5fdb76a3c7ead5cdfaee023ff48be8.js"></script>


###### 例子

>* [链式编程的例子：block 实现链式编程的例子（将block和method的特性 结合起来） ](https://github.com/zhangkn/DKUsingblockImplementChainProgramming)
>* block 的妙用：结合block和方法的优点实现iOS的链式编程
><script src="https://gist.github.com/zhangkn/f563193f99617e38218d03d2e60e9f08.js"></script>
>* Masonry 自动布局框架中最明显体现这个特点
><script src="https://gist.github.com/zhangkn/1bb9f96a0136131925a6f99f5a467058.js"></script>
>

#### 函数式编程思想

**函数式编程思想**：是把操作尽量写成一系列嵌套的函数或者方法调用。

>* `特点`：每个方法必须有返回值（本身对象）,把函数或者Block当做参数,block参数（需要操作的值）block返回值（操作结果）
><script src="https://gist.github.com/zhangkn/2c7c928ef54ece857adb947491df348e.js"></script>

>* `代表`：**ReactiveCocoa**
><script src="https://gist.github.com/zhangkn/2c7c928ef54ece857adb947491df348e.js"></script>
>
>* `实现`：用函数式编程实现，写一个加法计算器,并且加法计算器自带判断是否等于某个值.
><script src="https://gist.github.com/zhangkn/2f5b482402385de5d47c7d8b0803d9f6.js"></script>
>
>

#### **ReactiveCocoa** 结合了这两种种编程风格

- **函数式编程**（Functional Programming）

- **响应式编程**（Reactive Programming）

所以，你可能听说过 **ReactiveCocoa** 被描述为函数响应式编程（FRP）框架。

以后使用RAC解决问题，就不需要考虑调用顺序，直接考虑结果，把每一次操作都写成一系列嵌套的方法中，使代码高聚合，方便管理。

# 导入ReactiveCocoa
---


>ReactiveCocoa的[GitHub地址](https://github.com/ReactiveCocoa/ReactiveCocoa)

#### Objective-C 

>* 关于 ReactiveCocoa 的版本演进历程，简单介绍如下：
>```
><= v2.5 ：Objective-C ； 
v3.x ：Swift 1.2 ； 
v4.x ：Swift 2.x 。
>```

**ReactiveCocoa 2.5**版本以后改用了**Swift**，所以**Objective-C**项目需要导入**2.5版本**
>* `CocoaPods`集成：
```
platform :ios, '8.0'
target 'YouProjectName' do
use_frameworks!
pod 'ReactiveCocoa', '~> 2.5'
end
```


#### Swift

>* **Swift**项目导入最新版本
```
platform :ios, '8.0'
target 'YouProjectName' do
use_frameworks!
pod 'ReactiveCocoa'
end
```

>* 头文件即可
>
```
#import <ReactiveCocoa/ReactiveCocoa.h>
```


# [ReactiveCocoa常见类](https://img-blog.csdn.net/20170720145410358?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvejkyOTExODk2Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![](https://ws2.sinaimg.cn/large/006tKfTcgy1fr3xbnfznaj31kw18245s.jpg)


>* ReactiveCocoa就是根据 Monad 的概念搭建起来的
>```
>一个 Monad 就是一种实现了 Monad typeclass 的数据类型。 
```

#### ReactiveCocoa 主要由以下四大核心组件构成：

>* 信号源：RACStream 及其子类； 
>```
>1)信号源又是最核心的部分，其他组件都是围绕它运作的
>2) 它使用信号来代表这些异步事件，提供了一种统一的方式来处理所有异步的行为，包括代理方法、block 回调、target-action 机制、通知、KVO 等：
>```
><script src="https://gist.github.com/zhangkn/5d9ba1a80977802fb3d97a55d5d1b77f.js"></script>
>* 订阅者：RACSubscriber 的实现类及其子类； 
>* 调度器：RACScheduler 及其子类； 
>* 清洁工：RACDisposable 及其子类。


#### RACSiganl 信号类

>* 信号源代表的是随着时间而改变的值流。
>```
>信号类,一般表示将来有数据传递，只要有数据改变，信号内部接收到数据，就会马上发出数据
>```

>* 注意：对于 RACSignal 类簇来说，最核心的方法莫过于 -subscribe
>```
>1) 信号类(RACSiganl)，只是表示当数据改变时，信号内部会发出数据，它本身不具备发送信号的能力，而是交给内部一个订阅者去发出。
>2) 默认一个信号都是冷信号，也就是值改变了，也不会触发，只有订阅了这个信号，这个信号才会变为热信号，值改变了才会触发。
>-3) 如何订阅信号：调用信号RACSignal的subscribeNext就能订阅
>```
```

>* RACSignal 代表的是未来将会被传送的值，它是一种 push-driven 的流。RACSignal 可以向订阅者发送三种不同类型的事件：
><script src="https://gist.github.com/zhangkn/6a34f90993af21f4ab4791d01bcd5f00.js"></script>


>* [使用：KNTestReactiveCocoa/KNTestReactiveCocoa/KNTestRACTool.m](https://github.com/zhangkn/KNTestReactiveCocoa/blob/d549c29303cb1d2422d055f1c9a3f4d36d8bf3c9/KNTestReactiveCocoa/KNTestRACTool.m)
><script src="https://gist.github.com/zhangkn/fb2c333681165495cef8827bf1b8d012.js"></script>
>
>
#### RACStream



>* RACStream 是 ReactiveCocoa 中最核心的类，代表的是任意的值流，它是整个 ReactiveCocoa 得以建立的基石，下面是它的继承结构图：
>![](https://ws3.sinaimg.cn/large/006tKfTcgy1fr3zw0b9b0j315g0q8wgf.jpg)
> 





#### RACSubscriber

>* 表示订阅者的意思，用于发送信号，这是一个协议，不是一个类，只要遵守这个协议，并且实现方法才能成为订阅者。
>
>* 通过create创建的信号，都有一个订阅者，帮助他发送数据。
>
>```objc
>+ (RACSignal *)createSignal:(RACDisposable * (^)(id<RACSubscriber> subscriber))didSubscribe;
>```
>

#### RACDisposable

>* 用于取消订阅或者清理资源，当信号发送完成或者发送错误的时候，就会自动触发它。
>```
>它封装了取消和清理一次订阅所必需的工作。它有一个核心的方法 -dispose ，调用这个方法就会执行相应的清理工作，这有点类似于 NSObject 的 -dealloc 方法。
>```

**使用场景**：不想监听某个信号时，可以通过它主动取消订阅信号。

>* RACDisposable 总共有四个子类，它的继承结构图如下
>![](https://ws4.sinaimg.cn/large/006tKfTcgy1fr403r72z3j30pg08cq3d.jpg)
><script src="https://gist.github.com/zhangkn/452a8e81d106fa3ab954b37792ad2315.js"></script>
>


#### RACSubject

>RACSubject:信号提供者，自己可以充当信号，又能发送信号。
>```
>可以把它看作是 RACSignal 的可变版本
>```
>![](https://ws1.sinaimg.cn/large/006tKfTcgy1fr40axzcvgj314q0e8dgk.jpg)
>
>
>* 在 MVVM 中使用 RACSubject 可以非常方便地实现统一的错误处理逻辑。
><script src="https://gist.github.com/zhangkn/9fe29ab27ce9ac05f848f910d9844afb.js"></script>
>


**使用场景**:通常用来代替代理，有了它，就不必要定义代理了。


#### **RACReplaySubject**

>重复提供信号类，RACSubject的子类。

`RACReplaySubject`与`RACSubject`区别:

 `RACReplaySubject`可以先发送信号，在订阅信号，`RACSubject`就不可以。

 **使用场景一**:如果一个信号每被订阅一次，就需要把之前的值重复发送一遍，使用重复提供信号类。
 
 **使用场景二**:可以设置capacity数量来限制缓存的value的数量,即只缓充最新的几个值。
 
[ **ACSubject** 和 **RACReplaySubject** 简单使用：](https://github.com/zhangkn/KNTestReactiveCocoa/blob/d549c29303cb1d2422d055f1c9a3f4d36d8bf3c9/KNTestReactiveCocoa/KNTestRACTool.m)

 

>* **RACSubject**替换代理（与block类似）
<script src="https://gist.github.com/zhangkn/1ab0f0b0d7e1aec589af5c134b30eb1a.js"></script>



#### RACTuple

>元组类,类似NSArray,用来包装值.(`@[key, value]`)


#### RACSequence

>* RACSequence 代表的是一个不可变的值的序列
>```
>CSequence 并不能算作是信号源，因为它并不能像 RACSignal 那样，可以被订阅者订阅，但是它与 RACSignal 之间可以非常方便地进行转换
>```

>* 从理论上说，一个 RACSequence 由两部分组成：
>```
>head ：指的是序列中的第一个对象，如果序列为空，则为 nil ；
>tail ：指的是序列中除第一个对象外的其它所有对象，同样的，如果序列为空，则为 nil 。
>事实上，一个序列的 tail 仍然是一个序列。
>```

>RAC中的集合类，用于代替NSArray,NSDictionary,可以使用它来快速遍历数组和字典。
><script src="https://gist.github.com/zhangkn/0b2ab2c03269efac5df171f1474d80e0.js"></script>



#### RACCommand

>RAC中用于处理事件的类，可以把事件如何处理,事件中的数据如何传递，包装到这个类中，他可以很方便的监控事件的执行过程。
><script src="https://gist.github.com/zhangkn/29c834fd4361f0333c41145b945cec1f.js"></script>
>

#### RACMulticastConnection

>* 用于当一个信号，被多次订阅时，为了保证创建信号时，避免多次调用创建信号中的block，造成副作用，可以使用这个类处理。
>```
>    // 需求：假设在一个信号中发送请求，每次订阅一次都会发送请求，这样就会导致多次请求。
    // 解决：使用RACMulticastConnection就能解决. 
>```
><script src="https://gist.github.com/zhangkn/cc1430b731cee7607ee1405694d3e52c.js"></script>
>



#### RACScheduler

>RAC中的队列，用GCD封装的。

#### RACUnit
>表⽰stream不包含有意义的值,也就是看到这个，可以直接理解为nil.

#### RACEven
>把数据包装成信号事件(signal event)。它主要通过RACSignal的-materialize来使用，然并卵。


# ReactiveCocoa开发中常见用法

>* 1. 替换代理	: rac_signalForSelector：用于替代代理。 
		
>* 2. 替换KVO: rac_valuesAndChangesForKeyPath：用于监听某个对象的属性改变。 

>* 3. 监听事件: rac_signalForControlEvents：用于监听某个事件。 

>* 4. 替换通知: rac_addObserverForName:用于监听某个通知。 

>* 5. 监听文本框文字改变: rac_textSignal:只要文本框发出改变就会发出这个信号。 

>* 6. 统一处理多个网络请求:rac_liftSelector:withSignalsFromArray:Signals:当传入的Signals(信号数组)，每一个signal都至少sendNext过一次，就会去触发第一个selector参数的方法。 
>```
>使用注意：几个信号，参数一的方法就几个参数，每个参数对应信号发出的数据。
>```

#### 替换代理：

**rac_signalForSelector:**

>* `rac_signalForSelector:` 直接监听 `Selector` 事件的调用
>```
>[[self
    rac_signalForSelector:@selector(webViewDidStartLoad:)
    fromProtocol:@protocol(UIWebViewDelegate)]
    subscribeNext:^(id x) {
        // 实现 webViewDidStartLoad: 代理方法
    }];
>```
>* RACSubject替换代理
><script src="https://gist.github.com/zhangkn/1ab0f0b0d7e1aec589af5c134b30eb1a.js"></script>
>


#### 替换KVO

>* **rac_valuesForKeyPath:**

```
// KVO
// 监听 slider 的 value 变化
[[self.slider rac_valuesForKeyPath:@"value" observer:nil] subscribeNext:^(id x) {

    NSLog(@"slider value Change：%@", x);
}];
```

>* RACObserve
>// KVO
[RACObserve(self, username) subscribeNext:^(NSString *username) {
    // 用户名发生了变化
}];

#### 替换通知

>* **rac_addObserverForName**
><script src="https://gist.github.com/zhangkn/11ec19e7f1e15f47764aea069de402bf.js"></script>
>


#### 监听事件

**rac_signalForControlEvents:**

```
// 监听 btn 的 UIControlEventTouchUpInside 点击事件
[[self.btn rac_signalForControlEvents:UIControlEventTouchUpInside] subscribeNext:^(id x) {
    
    NSLog(@"btnTap");
}];
```


#### 监听 textField 文字变化

**rac_textSignal**

```
[[self.textField rac_textSignal] subscribeNext:^(id x) {
        
	NSLog(@"textField change: %@", x);
}];
```

#### 统一处理多个网络请求

>* **rac_liftSelector:**
><script src="https://gist.github.com/zhangkn/1a42252416bb40c762194e6dc179f289.js"></script>




#### **注意**：

- `替换KVO`和 `监听文本框文字改变` 方法在创建监听方法时就会执行一次。
	
```
2016-12-28 16:53:50.746 ReactiveCacoa[4956:1246592] slider value Change：0.5
2016-12-28 16:53:50.748 ReactiveCacoa[4956:1246592] textField change:
```

- 使用`rac_liftSelector`时 `@selector(updateWithR1:R2:) `中的方 **参数个数** 要与 **signal个数** 相同，否则会被断言Crash！

# see also

>* [monad](http://blog.leichunfeng.com/blog/2015/11/08/functor-applicative-and-monad/)
>
>* [ReactiveCocoa](https://blog.csdn.net/z929118967/article/details/75220438)

>* [MVVM](https://blog.csdn.net/z929118967/article/details/75315257)
>* [介绍一个使用 MVVM 和 RAC 开发的开源项目 MVVMReactiveCocoa](https://blog.csdn.net/z929118967/article/details/75634648)
>
>* [最快让你上手ReactiveCocoa之基础篇](http://www.jianshu.com/p/87ef6720a096)
>* [ReactiveCocoa 讨论会](https://gold.xitu.io/entry/568bd2ae60b2e57ba2cd2c7b)
>



```