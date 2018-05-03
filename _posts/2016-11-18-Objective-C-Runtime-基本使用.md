---
layout:     post
title:      Objective-C_Runtime_Cases
subtitle:   Runtime的使用案例
date:       2017-02-04
author:     
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Runtime
--- 

# 前言

>* `#import <objc/runtime.h>`


- 查询方法
- 给分类添加属性
- Method Swizzling
- 动态添加方法
- 字典转属性


# 1、查询方法

#### 1、1 获取类的名称

>* 方法：`const char *object_getClassName(id obj)`

#### 1、2 获取类中的方法

>* 方法：`Method *class_copyMethodList(Class cls, unsigned int *outCount) `

# 2、给分类添加属性

####  2、1 给分类添加属性
---


>*  CMessageMgr+knkeyword.h
><script src="https://gist.github.com/zhangkn/4bdb0a2a46d53299b7da9abe2d783161.js"></script>
>
>* CMessageMgr+knkeyword.m
><script src="https://gist.github.com/zhangkn/d6e0758ba1560f71f16e3f25bf4eba7a.js"></script>
>


# 3、Method Swizzling

#### 3、1 Method Swizzling


>* [例子](https://gist.github.com/zhangkn/aa1e1704963fbd4b7e6cd1dcf4ab07e5)
><script src="https://gist.github.com/zhangkn/aa1e1704963fbd4b7e6cd1dcf4ab07e5.js"></script>

>* 结合@dynamic的 associatedObject例子
><script src="https://gist.github.com/zhangkn/6497ec50595c77f3a37f9f074948cc86.js"></script>
>

# 4、ynamic Method Resolution

借助 `setAssociatedObject` 和 `getAssociatedObject` 这两个函数，向既有的类当中添加存储属性。

####  4、1 动态添加方法(Dynamic Method Resolution)


>* [例子:动态添加resolveThisMethodDynamically](https://gist.github.com/zhangkn/9a8464db30e597d9ebb5291070b403f1)
><script src="https://gist.github.com/zhangkn/9a8464db30e597d9ebb5291070b403f1.js"></script>


####  4.2 知识补充

>* [方法转发：崩溃之前会预留几个步骤](https://gist.github.com/zhangkn/473dbdc08c094c21cbe824485362723f)
><script src="https://gist.github.com/zhangkn/473dbdc08c094c21cbe824485362723f.js"></script>
>




# 5、字典转模型


>* `MJExtension` 是我以前经常用的

# 6、XCTest 的最简单版本：借助 Objective-C 的运行时机制实现

>* XCTest 的最简单版本：借助 Objective-C 的运行时机制实现
><script src="https://gist.github.com/zhangkn/ffc9d8d63087c9c1caf65d37fd8c0cf7.js"></script>
>

# 7、运行时层面的上一层框架：Foundation 

>* KVC 和 KVO 允许我们将 UI 和数据进行绑定。这也是 Rx 以及其他响应式框架实现的基础。
><script src="https://gist.github.com/zhangkn/4f3475f0e5af98fa302f6bc6ac84d7e5.js"></script>


# 8、Swift 的动态性

>* @objc 和 @dynamic
>```
>1) @objc 将您的 Swift API 暴露给 Objective-C 运行时，但是它仍然不能保证编译器会尝试对其进行优化。
2) 想使用动态功能的话，就需要使用 @dynamic。（一旦您使用了 @dynamic 修饰符之后，就不需要添加 @objc 了，因为它已经隐含在其中。）
>```


#### 8.1 Swift 当中的动态特性

>* 方法转发
>```
>// 1
override class func resolveInstanceMethod(_ sel: Selector!)
-> Bool {
    // 添加实例方法并返回 true 的一次机会，它随后会再次尝试发送消息
}
// 2
override func forwardingTarget(for aSelector: Selector!) ->
Any? {
    // 返回可以处理 Selector 的对象
}
// 3 - Swift 不支持 NSInvocation
>```


>* 方法混淆
>```
>方法混淆
1) load 在 Swift 不再会被调用，因此我们需要在 initialize 中进行混淆。
2) 在 Objective-C 当中，我们使用 dispatch_once，但是自 Swift 3 之后，dispatch_once 便不复存在于 Swift 当中了。事情变得略为复杂。
>```

>* 内省
```
if self is MyClass {
    // YAY
}
let myString = "myString";
let mirror = Mirror(reflecting: myString)
print(mirror.subjectType) // “String"
let string = String(reflecting: type(of:
myString)) // Swift.String
```


>* `is` 替代了 `isMemberOfClass`
>
>

# see also

>* [Objective-C 运行时以及 Swift 的动态性](https://segmentfault.com/a/1190000012362645)
>