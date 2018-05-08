---
layout:     post
title:      OC_Runtime_system
subtitle:    read this document to gain an understanding of how the Objective-C runtime system works and how you can take advantage of it.
date:       2017-02-04
author:     
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - objc
    - Runtime
    
--- 

# [前言](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048?language=objc)


#### runtime
>* Objective-C 是一门基于运行时的编程语言，
>```
>1)这意味着所有方法、变量、类之间的link，都会推迟到应用实际运行的最后一刻才会建立。
> 2)Foundation 框架实现了基于运行时的一个特性：
键值编码 (key-value-coding, KVC) 以及键值观察 (key-value observing, KVO)。
KVC 和 KVO 允许我们将 UI 和数据进行绑定。这也是 Rx 以及其他响应式框架实现的基础。
ps:而且MVVM的实现又可以借助“V-VM”第三方绑定框架进行实现
>```


>* 对象在 runtime.h 当中是这样定义的：
><script src="https://gist.github.com/zhangkn/f5ed829af985c625e1cc054494643f57.js"></script>


#### runtime code

>* [runtime源码:它是完全开源的，并且开源了很长一段时间了](https://opensource.apple.com/source/objc4/objc4-208/runtime/)
>```
>https://opensource.apple.com/source/objc4/objc4-208/
>```
>
>* [ a git archive of all of the source code from http://opensource.apple.com/tarballs/ ](https://github.com/zhangkn/opensource.apple.com)
>
>* [github.com opensource-apple/objc4](https://github.com/opensource-apple/objc4/tree/master/runtime)
>
>* [github.com opensource-apple](https://github.com/opensource-apple)



# Who Should Read This Document


The document is intended for readers who might be interested in learning about the Objective-C runtime.


# Organization of This Document


>* [Runtime Versions and Platforms](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtVersionsPlatforms.html#//apple_ref/doc/uid/TP40008048-CH106-SW1)
>* [Interacting with the Runtime](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtInteracting.html#//apple_ref/doc/uid/TP40008048-CH103-SW1)
>* [Messaging](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)
>* [Dynamic Method Resolution](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtDynamicResolution.html#//apple_ref/doc/uid/TP40008048-CH102-SW1)
>* [Message Forwarding](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtForwarding.html#//apple_ref/doc/uid/TP40008048-CH105-SW1)
>* [Type Encodings](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100-SW1)
>* [Declared Properties](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtPropertyIntrospection.html#//apple_ref/doc/uid/TP40008048-CH101-SW1)



# I、 Runtime Versions and Platforms

#### Legacy and Modern Versions

>* In the legacy runtime
>```
>In the legacy runtime, if you change the layout of instance variables in a class, you must recompile classes that inherit from it
>```

>* In the modern runtime
>```
>In the modern runtime, if you change the layout of instance variables in a class, you do not have to recompile classes that inherit from it.
>```



# II、Interacting with the Runtime

>* Objective-C programs interact with the runtime system at three distinct levels
>```
> 1) through Objective-C source code; 
> 2) through methods defined in the NSObject class of the Foundation framework;
> 3) and through direct calls to runtime functions.
>```

#### 0、Objective-C Source Code

For the most part, the runtime system works automatically and behind the scenes. You use it just by writing and compiling Objective-C source code.


#### 1、NSObject Methods: 「内省 (introspection)」机制：判别这个类能执行何种操作


>* `NSProxy`
```
@class NSMethodSignature, NSInvocation;
NS_ASSUME_NONNULL_BEGIN
NS_ROOT_CLASS
@interface NSProxy <NSObject> {
    Class	isa;
}
```

>* isKindOfClass:和isMemberOfClass:则检查对象是否在指定的类继承体系中
>```
>which test an object’s position in the inheritance hierarchy
>```
>* `respondsToSelector:`检查对象能否响应指定的消息；
>```
> which indicates whether an object can accept a particular message
>```
>* `conformsToProtocol:`检查对象是否实现了指定协议类的方法；
>```
>which indicates whether an object claims to implement the methods defined in a specific protocol
>```
>*`methodForSelector:`则返回指定方法实现的地址
>```
>which provides the address of a method’s implementation. Methods like these give an object the ability to introspect about itself.
>```

#### 3、Runtime Functions


The runtime system is a dynamic shared library with a public interface consisting of a set of functions and data structures in the header files located within the directory /usr/include/objc.

>* /usr/include/objc
>```
>devzkndeMacBook-Pro:bin devzkn$ cd  /usr/include/objc
devzkndeMacBook-Pro:objc devzkn$ tree -L 2
.
├── List.h
├── NSObjCRuntime.h
├── NSObject.h
├── Object.h
├── ObjectiveC.apinotes
├── Protocol.h
├── hashtable.h
├── hashtable2.h
├── message.h
├── module.modulemap
├── objc-api.h
├── objc-auto.h
├── objc-class.h
├── objc-exception.h
├── objc-load.h
├── objc-runtime.h
├── objc-sync.h
├── objc.h
└── runtime.h
>```


>* Objective-C runtime library support functions are implemented in the shared library found at /usr/lib/libobjc.A.dylib.
>
>* All of these functions are documented in [Objective-C Runtime Reference](https://developer.apple.com/documentation/objectivec/objective_c_runtime).



# III、[Messaging](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtHowMessagingWorks.html#//apple_ref/doc/uid/TP40008048-CH104-SW1)


 how the message expressions are converted into [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) function calls, and how you can refer to methods by name？
 



#### 3.1、The objc_msgSend Function


>* [`objc_msgSend `](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
>
```
Sends a message with a simple return value to an instance of a class.
objc_msgSend(id _Nullable self, SEL _Nonnull op, ...)
1) self : A pointer that points to the instance of the class that is to receive the message.
2) op : The selector of the method that handles the message.
3) ... :A variable argument list containing the arguments to the method.
```

>* Note: `isa` variable
>```
> the isa pointer is required for an object to work with the Objective-C runtime system. An object needs to be “equivalent” to a struct objc_object (defined in objc/objc.h) in whatever fields the structure defines. 
>```

>* Messaging Framework
>![](https://ws2.sinaimg.cn/large/006tNc79gy1fqxyuh7ri6g30980f4aa3.gif)
>```
>1) When a message is sent to an object, the messaging function follows the object’s isa pointer to the class structure where it looks up the method selector in the dispatch table. 
>If it can’t find the selector there, objc_msgSend follows the pointer to the superclass and tries to find the selector in its dispatch table. Successive failures cause objc_msgSend to climb the class hierarchy until it reaches the NSObject class. Once it locates the selector, the function calls the method entered in the table and passes it the receiving object’s data structure.
>```
>
>
#### 3.2、Using Hidden Arguments

They’re inserted into the implementation when the code is compiled.

>* When objc_msgSend finds the procedure that implements a method, it calls the procedure and passes it all the arguments in the message. It also passes the procedure two hidden arguments:
>```
>1) The receiving object
>2) The selector for the method
>```


>*  A method refers to the receiving object as self, and to its own selector as _cmd. In the example below, _cmd refers to the selector for the strange method and self to the object that receives a strange message.
>```
>strange
{
    id  target = getTheReceiver();
    SEL method = getTheMethod();
    if ( target == self || method == _cmd )
        return nil;
    return [target performSelector:method];
}
self is the more useful of the two arguments. It is, in fact, the way the receiving object’s instance variables are made available to the method definition.
>```

#### 3.3 Getting a Method Address



>* The example below shows how the procedure that implements the setFilled: method might be called:
><script src="https://gist.github.com/zhangkn/9b57c2fd678df8897bb654a2da3cdf90.js"></script>
>
>


# IV、[Dynamic Method Resolution](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtDynamicResolution.html#//apple_ref/doc/uid/TP40008048-CH102-SW1)



 how you can provide an implementation of a method dynamically?
 
 
#### 4.1 Dynamic Method Resolution


>* For example, the Objective-C declared properties feature includes the @dynamic directive:
>```
>@dynamic propertyName;
>//which tells the compiler that the methods associated with the property will be provided dynamically.
>```


>* [Adds a new method to a class with a given name and implementation.](https://developer.apple.com/documentation/objectivec/1418901-class_addmethod)
><script src="https://gist.github.com/zhangkn/eb415d8dd908d5b73a292334cadaa18a.js"></script>

>*  dynamically add `dynamicMethodIMP ` to a class as a method  using `resolveInstanceMethod`
><script src="https://gist.github.com/zhangkn/9a8464db30e597d9ebb5291070b403f1.js"></script>



>* If` respondsToSelector:` or `instancesRespondToSelector: `is invoked, the dynamic method resolver is given the opportunity to provide an `IMP` for the selector first. If you implement `resolveInstanceMethod:` but want particular selectors to actually be forwarded via the forwarding mechanism, you return NO for those selectors.
>```
>动态方法解析会在消息转发机制浸入前执行。如果 respondsToSelector: 或 instancesRespondToSelector: 方法被执行，动态方法解析器将会被首先给予一个提供该方法选择器对应的 IMP 的机会。如果你想让该方法选择器被传送到转发机制，那么就让resolveInstanceMethod: 返回 NO 
>```

>* [ Objective-C Runtime Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048) > [Type Encodings](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100)
>* [resolveClassMethod](https://developer.apple.com/documentation/objectivec/nsobject/1418889-resolveclassmethod?language=objc)
>* [resolveInstanceMethod](https://developer.apple.com/documentation/objectivec/nsobject/1418500-resolveinstancemethod?language=objc)
>


#### 4.2 Dynamic Loading

Although there is a runtime function that performs dynamic loading of Objective-C modules in Mach-O files (`objc_loadModules`, defined in `objc/objc-load.h`), Cocoa’s NSBundle class provides a significantly more convenient interface for dynamic loading—one that’s object-oriented and integrated with related services. 

>* See the [`NSBundle`](https://developer.apple.com/documentation/foundation/bundle) class specification in the Foundation framework reference for information on the NSBundle class and its use. 
>* See OS X ABI `Mach-O File Format Reference` for information on Mach-O files.
>


# V 、 Message Forwarding


before announcing the error, the runtime system gives the receiving object a second chance to handle the message.


#### 5.0 重定向

>* 在消息转发机制执行前，Runtime 系统会再给我们一次偷梁换柱的机会:即通过重载`- (id)forwardingTargetForSelector:(SEL)aSelector` 方法替换消息的接受者为其他对象：
><script src="https://gist.github.com/zhangkn/a6c1dfc780cafe966d7e0167c0967ae6.js"></script>
>




#### 5.1 Forwarding



>* If you send a message to an object that does not handle that message,
>```
>before announcing an error the runtime sends the object a `forwardInvocation:` message with an `NSInvocation` object as its sole argument—the `NSInvocation` object encapsulates the original message and the arguments that were passed with it.
>```
>* You can implement a forwardInvocation: method to give a default response to the message, or to avoid the error in some other way.
>```
> As its name implies, forwardInvocation: is commonly used to forward the message to another object.
```


>* 1) simply passes the message on to an instance of the other class
><script src="https://gist.github.com/zhangkn/ae30c1e9069b76dbba25cf4b4808dde7.js"></script>
>
>



>* 2) The second chance offered by a forwardInvocation: message provides a less ad hoc solution to this problem, and one that’s dynamic rather than static.
>```
当动态方法解析不作处理返回NO时，消息转发机制会被触发。在这时`forwardInvocation:`方法会被执行
```
><script src="https://gist.github.com/zhangkn/bc3cfae0f03d5ebaceb2f7f67dcbf6bf.js"></script>
>



#### 5.2 Forwarding and Multiple Inheritance(转发和多继承)


>* 转发和继承相似，可以用于为Objc编程添加一些多继承的效果。就像下图那样，一个对象把消息转发出去，就好似它把另一个对象中的方法借过来或是“继承”过来一样。
>![](https://ws1.sinaimg.cn/large/006tNc79gy1fqy5qfrb8ag30ag06pq2t.gif)


>* In this illustration, an instance of the Warrior class forwards a negotiate message to an instance of the Diplomat class. The Warrior will appear to negotiate like a Diplomat. It will seem to respond to the negotiate message, and for all practical purposes it does respond (although it’s really a Diplomat that’s doing the work).
>```
>图中 `Warrior` 和 `Diplomat` 没有继承关系，但是 `Warrior` 将`negotiate` 消息转发给了 `Diplomat` 后，就好似 `Diplomat` 是 `Warrior` 的超类一样。
>```



#### 替代者对象(Surrogate Objects)


>* Forwarding not only mimics multiple inheritance, it also makes it possible to develop lightweight objects that represent or “cover” more substantial objects. The surrogate stands in for the other object and funnels messages to it.



#### Forwarding and Inheritance

>* an object is able to forward a message to its surrogate, you would implement methodSignatureForSelector:
><script src="https://gist.github.com/zhangkn/5f4daeb208f39ad959e369c0806e9e72.js"></script>



# VI 、Declared Properties

#### Property Type and Functions

>* The Property structure defines an opaque handle to a property descriptor.
>```
>typedef struct objc_property *Property;
>```
>
>* You can use the functions class_copyPropertyList and protocol_copyPropertyList to retrieve an array of the properties associated with a class (including loaded categories) and a protocol respectively:
>```
>objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount)
objc_property_t *protocol_copyPropertyList(Protocol *proto, unsigned int *outCount)
>```
>
>* print a list of all the properties associated with a class using the following code:
><script src="https://gist.github.com/zhangkn/55bd9540b5a8e907b6dd128a7bceaecc.js"></script>
>
>




# VII、Objective-C Associated Objects


```
 void objc_setAssociatedObject ( id object, const void *key, id value, objc_AssociationPolicy policy );
 id objc_getAssociatedObject ( id object, const void *key );
 void objc_removeAssociatedObjects ( id object );
```


这些方法以键值对的形式动态地向对象添加、获取或删除关联值。其中关联政策是一组枚举常量：

```
enum {
   OBJC_ASSOCIATION_ASSIGN  = 0,
   OBJC_ASSOCIATION_RETAIN_NONATOMIC  = 1,
   OBJC_ASSOCIATION_COPY_NONATOMIC  = 3,
   OBJC_ASSOCIATION_RETAIN  = 01401,
   OBJC_ASSOCIATION_COPY  = 01403
};
```

>* 例子： 动态添加属性的例子:关联对象
><script src="https://gist.github.com/zhangkn/e427935b259e86a7f9058a0dc57fc489.js"></script>
>

# VIII、Method Swizzling


>* 友盟统计模块的例子，使用Swizzle
><script src="https://gist.github.com/zhangkn/aa1e1704963fbd4b7e6cd1dcf4ab07e5.js"></script>
 



# IX、Class-Definition Data Structures

>* /usr/include/objc
 
>* include/objc/objc.h
 ```
 /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/usr/include/objc/objc.h
```

#### SEL

Selectors generated by the compiler are automatically mapped by the runtime when the class is loaded.
You can add new selectors at runtime and retrieve existing selectors using the function `sel_registerName`



>* SEL /// An opaque type that represents a method selector.
><script src="https://gist.github.com/zhangkn/38f514da906eb6559956a54300021601.js"></script>

>* Discussion
>```
>When using selectors, you must use the value returned from sel_registerName or the Objective-C compiler directive @selector(). You cannot simply cast a C string to SEL.
>```

#### objc_method_list

>* Contains an array of method definitions.
>```
>struct objc_method_list {
    struct objc_method_list *obsolete;
    int 
method_count;
    struct objc_method 
method_list[1];
};
>```

#### objc_method_description

>* Defines an Objective-C method.
>```
>struct objc_method_description {
    SEL name;
    char *
types;
};
>```



####  isa

The isa pointer, as the name suggests, points to the object's class which maintains a dispatch table. This dispatch table essentially contains pointers to the methods the class implements, among other data.

>* Note: `isa` variable
>```
> the isa pointer is required for an object to work with the Objective-C runtime system. An object needs to be “equivalent” to a struct objc_object (defined in objc/objc.h) in whatever fields the structure defines. 
>```

>* [Key-Value Observing Implementation Details:Automatic key-value observing is implemented using a technique called isa-swizzling](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/Articles/KVOImplementation.html#//apple_ref/doc/uid/20002307-BAJEAIEE)
>```
>You should never rely on the isa pointer to determine class membership. Instead, you should use the class method to determine the class of an object instance.
>1) When an observer is registered for an attribute of an object the isa pointer of the observed object is modified, pointing to an intermediate class rather than at the true class. As a result the value of the isa pointer does not necessarily reflect the actual class of the instance.
>```


#### objc_class


>* objc_class
><script src="https://gist.github.com/zhangkn/e427935b259e86a7f9058a0dc57fc489.js"></script>
>* class-diagram
>![](https://ws1.sinaimg.cn/large/006tNc79ly1fqy2tsj6pxj30px0r5wfl.jpg)


#### objc_object

>* /// Represents an instance of a class.
><script src="https://gist.github.com/zhangkn/4ff01a8c94c7f64641bc4c9f3db7aa0f.js"></script>


#### id


>* /// A pointer to an instance of a class.
```
typedef struct objc_object *id;
```


#### [Class](https://developer.apple.com/documentation/objectivec/1418956-nsobject/1571949-class)

>* /// An opaque type that represents an Objective-C class.
```
typedef struct objc_class *Class;
```

#### Method


>* /// An opaque type that represents a method in a class definition.
```
typedef struct objc_method *Method;
```

#### objc_method

><script src="https://gist.github.com/zhangkn/d4b4ae2de21f2d27eb313c1b3fa74266.js"></script>

#### Ivar

>* /// An opaque type that represents an instance variable.
```
typedef struct objc_ivar *Ivar;
```

####  objc_ivar




>* <script src="https://gist.github.com/zhangkn/2f39d245b51afa3d1aea48cfde2b3778.js"></script>


>* 可以根据实例查找其在类中的名字，也就是“反射”：
><script src="https://gist.github.com/zhangkn/3e6ec52fccb777bc11e6704e13a70b92.js"></script>
>
>

#### IMP

>* /// A pointer to the function of a method implementation. 
><script src="https://gist.github.com/zhangkn/e7a221ee55f1e7dec07ef9445883c949.js"></script>
>

#### [Cache](https://developer.apple.com/documentation/objectivec/objective_c_runtime/objc_cache?language=objc)


>* objc_cache
```
typedef struct objc_cache *Cache
```

#### objc_cache

Performance optimization for method calls. Contains pointers to recently used methods.


>* objc_cache
><script src="https://gist.github.com/zhangkn/50501ed1f84eb847cfcdf1ee5cf4fb6a.js"></script>



#### Category

>* An opaque type that represents a category.
>```
>typedef struct objc_category *Category;
>```

#### objc_protocol_list

>* Represents a list of formal protocols.
>```
>struct objc_protocol_list {
    struct objc_protocol_list *next;//A pointer to another objc_protocol_list data structure.
    long 
count;//The number of protocols in this list.
    Protocol * _Nullable 
list[1];//An array of pointers to 
Class
 data structures that represent protocols.
};
>```

#### objc_property_attribute_t

>* Defines a property attribute.
>


#### [objc4/runtime/Messengers.subproj/objc-msg-arm.s](https://github.com/opensource-apple/objc4/blob/master/runtime/Messengers.subproj/objc-msg-arm.s)

这个文件是汇编写成的.



#### objc_property_t

>* objc_property_t
```
typedef struct objc_property *Property;
typedef struct objc_property *objc_property_t;///// An opaque type that represents an Objective-C declared property.
```




#### objc_super

>* Specifies the superclass of an instance. 
><script src="https://gist.github.com/zhangkn/be110239ba7d76aeb2960fb91ff21d5e.js"></script>


# X、Objective-C Runtime Reference


>* Objective-C runtime library support functions are implemented in the shared library found at /usr/lib/libobjc.A.dylib.


#### Topics

###### 10.1 Working with Classes

>* class_getName
>```
>Returns the name of a class.
>```

###### 10.2 Adding Classes

>* objc_allocateClassPair
>```
Creates a new class and metaclass.
```

###### 10.3 Instantiating Classes

>* class_createInstance
>```
Creates an instance of a class, allocating memory for the class in the default malloc memory zone.
```

###### 10.4 Working with Instances

>* object_getClassName
>```
>object_getClassName
Returns the class name of a given object.
>```

###### 10.5 Obtaining Class Definitions

>* objc_getClassList
>```
>objc_getClassList
Obtains the list of registered class definitions.
>```

###### 10.6 Working with Instance Variables

>* ivar_getOffset
>```
>ivar_getOffset
Returns the offset of an instance variable.
>```


###### 10.6 Associative References

>* [AssociatedObject,常用来新增属性](https://kunnan.github.io/2017/02/04/Objective-C_Runtime_system/#viiobjective-c-associated-objects)
>```
1) objc_setAssociatedObject
Sets an associated value for a given object using a given key and association policy.
2) objc_getAssociatedObject
Returns the value associated with a given object for a given key.
3) objc_removeAssociatedObjects
Removes all associations for a given object.
>```

###### 10.7  Sending Messages


When it encounters a method invocation, the compiler might generate a call to any of several functions to perform the actual message dispatch, depending on the receiver, the return value, and the arguments. You can use these functions to dynamically invoke methods from your own plain C code, or to use argument forms not permitted by NSObject’s perform... methods. These functions are declared in /usr/include/objc/objc-runtime.h.

>* `objc_msgSend` sends a message with a simple return value to an instance of a class.

>* `objc_msgSend_stret` sends a message with a data-structure return value to an instance of a class.

>* `objc_msgSendSuper` sends a message with a simple return value to the superclass of an instance of a class.

>* `objc_msgSendSuper_stret` sends a message with a data-structure return value to the superclass of an instance of a class.


###### [10.8 Working with Methods](https://kunnan.github.io/2017/02/04/Objective-C_Runtime_system/#viiimethod-swizzling)

[常用来method-swizzling](https://kunnan.github.io/2017/02/04/Objective-C_Runtime_system/#viiimethod-swizzling)



>* method_getImplementation
>```
>method_getImplementation
Returns the implementation of a method.
>```


###### 10.9 Working with Libraries

>* class_getImageName
>```
>class_getImageName
Returns the name of the dynamic library a class originated from.
>```
>

###### 10.10 Working with Selectors

>* [sel_registerName](https://developer.apple.com/documentation/objectivec/1418557-sel_registername?language=objc)
>```
Registers a method with the Objective-C runtime system, maps the method name to a selector, and returns the selector value.
>```

###### 10.11 Working with Protocols

>* objc_getProtocol
>```
>objc_getProtocol
Returns a specified protocol.
>```

###### 10.12 Working with Properties

>* property_getAttributes
>```
>property_getAttributes
Returns the attribute string of a property.
>```

###### 10.13 Using Objective-C Language Features

>* imp_getBlock
>```
>imp_getBlock
Returns the block associated with an IMP that was created using 
imp_implementationWithBlock
>```


###### 10.14 [Class-Definition Data Structures](https://kunnan.github.io/2017/02/04/Objective-C_Runtime_system/#ixclass-definition-data-structures)

>* [Class](https://developer.apple.com/documentation/objectivec/class?language=objc)
>```
>Class
An opaque type that represents an Objective-C class.
>```

###### 10.15 Instance Data Types

>* These are the data types that represent objects, classes, and superclasses.

>* `id` pointer to an instance of a class.

>* `id` represents an instance of a class.

>* `objc_super` specifies the superclass of an instance.


###### 10.16 Associative References
>* ` objc_AssociationPolicy ` Type to specify the behavior of an association.


# XI、See Also


>* [Objective-C 运行时以及 Swift 的动态性](https://segmentfault.com/a/1190000012362645)
>


>* [Objective-C Runtime Reference ](https://developer.apple.com/documentation/objectivec/objective_c_runtime)
>```
>describes the data structures and functions of the Objective-C runtime support library. Your programs can use these interfaces to interact with the Objective-C runtime system. For example, you can add classes or methods, or obtain a list of all class definitions for loaded classes.
>```
>

>* [Objective-C Runtime  Reference ](http://yulingtianxia.com/blog/2014/11/05/objective-c-runtime/)
>* [Objective-C Runtime Programming Guide](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048)
>* [Objective-C Runtime源码](https://opensource.apple.com/source/objc4/)
>*  [Objective-C runtime之运行时的基本特点](http://blog.csdn.net/wzzvictory/article/details/8615569)
>* [Understanding the Objective-C Runtime](http://cocoasamurai.blogspot.jp/2010/01/understanding-objective-c-runtime.html)
>
