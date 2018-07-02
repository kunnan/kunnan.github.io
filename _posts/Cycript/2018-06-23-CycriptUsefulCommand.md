---
layout: post
title: CycriptUsefulCommand
date: 2018-06-23
tag: Cycript
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 常用的cycript命令、分析思路
---


# [前言](http://iphonedevwiki.net/index.php/Cycript)

使用cy 分析应用：查看界面、查找关键函数、验证代码执行效果

# I 、Powerful private methods

>* Powerful private methods

```
_ivarDescription
_shortMethodDescription
nextResponder
_autolayoutTrace
recursiveDescription
_methodDescription
```

#### 1.0   定位特定的class

> * choose():get an array of existing objects of a certain class(当前堆栈中查找到特定类的对象数据)

```
cy# choose(UIButton).toString()
```

#### 1、1 定位`view`

>* ViewControlle(Shortcut to find the ViewController’s class name on the keyWindow): _printHierarchy
```
[[[UIWindow keyWindow] rootViewController] _printHierarchy].toString()
```

>* cy# [#0x181009f0 nextResponder]

>* 打印出当前界面的view层级
```
cy# UIApp.keyWindow.recursiveDescription().toString()
```
or 
```
 [UIWindow keyWindow].recursiveDescription().toString()
```

> * _autolayoutTrace:基于layout展示View架构
>
>   ```
>   [[UIApp keyWindow] _autolayoutTrace].toString()
>   ```
>
>   ```
>   cy# [[UIWindow keyWindow] _autolayoutTrace].toString()
>   ```



#### 1.2  定位 `按钮地址`


>*  `[UIWindow keyWindow].recursiveDescription().toString()` 展开views结构
```
[UIWindow keyWindow].recursiveDescription().toString()
```

>* [按钮文字unicode转码](http://tool.chinaz.com/tools/unicode.aspx)
>
>  ```
>  1、或者直接使用echo 进行查看即可
>   echo -e "\xe8\x95\xbe\xe8\x95\xbe"
>  2、 或者使用py进行查看中文
>  devzkndeMacBook-Pro:2018wxrobot devzkn$ python
>  Python 3.7.0b2 (default, Apr  4 2018, 15:42:50) 
>   print ("\u901a\u8baf\u5f55")
>   Use exit() or Ctrl-D (i.e. EOF) to exit
>  3. devzkndeMacBook-Pro:2018wxrobot devzkn$ /usr/bin/python
>  Python 2.7.10 (default, Jul 15 2017, 17:16:57) 
>  >>> print "\xe8\x95\xbe\xe8\x95\xbe"
>  >>> print u'\u901a\u8baf\u5f55'
>  ```
>
>  

```
\u4e8c\u7ef4\u7801\u6536\u6b3e 进行搜索找到对应按钮; 或者通过按钮的frame 也可以大概的猜出
```

> * 定位UIButton class 对应的对象，返回的是数组
>
>   ```
>   cy# choose(UIButton).toString() 
>   ```
>
>   

#### 1.3 定位`对象属性`

>* _ivarDescription: Prints all names and values of instance variables of a specified object
```
cy# [#0x5822600 _ivarDescription].toString()
```

#### 1.4 定位一些基本信息和路径 

> 
>
> * 使用`?exit` 或者`control+d` 进行退出
>
> * bundleIdentifier
>
>   ```
>   [[NSBundle mainBundle] bundleIdentifier]
>   ```
>
> * 沙盒
>
>   ```
>   [[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask][0]
>   ```
>
>   ```
>   FOUNDATION_EXPORT NSString *NSHomeDirectory(void);
>   cy# [NSHomeDirectory()]
>   [@"/var/mobile/Containers/Data/Application/93E1B7BE-8916-411C-9228-35B9FD8DA825"]
>   ```
>
>   

# II、 快速定位按钮对应的`allTargets `、`allControlEvents `、 `actionsForTarget `


#### 2.1  获取actionsForTarget的步骤

>* 2.1.1 获取allTargets
```
cy# [[#0x170bab80 allTargets] allObjects][0]
#"<WCPayOfflinePayViewController: 0x1642b200>"
```
>* 2.1.2 获取 actions
```
cy# [#0x170bab80 actionsForTarget:[[#0x170bab80 allTargets] allObjects][0] forControlEvent:[#0x170bab80 allControlEvents]]
@["receiveMoneyBtnPress:"]
```

>* 2.1.3 NSLog: `allTargets `,`actionsForTarget`
>```       
>NSLog(@"allTargets: %@ actionsForTarget :%@",[normalRedEnvelopesButton allTargets],[normalRedEnvelopesButton actionsForTarget:[[normalRedEnvelopesButton allTargets] allObjects][0]forControlEvent:[normalRedEnvelopesButton allControlEvents]]);
>```

#### 2.2  验证`执行方法`

###### objc_msgSend 



>* `objc_msgSend` : 好处： 不需要声明方法，即可执行使用
>  
>
>  ```
>   objc_msgSend(vc, @selector(initView));//  执行实例方法
>  ```
>
>  ​        
>
>  ```
>  objc_msgSend(objc_getClass("CContactMgr"), sel_registerName("setupdoVerifybywxid:greetings:"),@"wmbhny722",@"hi");// 发送多个参数的函数
>  ```
```
    objc_msgSend(objc_getClass("FTSContactMgr"), sel_registerName("setupgroupsyncdata"));//执行类方法，没有参数。
```
    id data = objc_msgSend(MainFrameLogic, @selector(getSessionInfoByContact:), contact);
> * []
> * cy# [[[#0x170bab80 allTargets] allObjects][0] receiveMoneyBtnPress:nil]

###### valueForKey: 利用KVC 获取属性

>  ```
>  //利用属性的偏移加对象的地址来获取属性的对象
>      Ivar t_rightViewsm_mainFrameViewController_scrollViewivar = class_getInstanceVariable(objc_getClass("MMUINavigationBar"), "_rightViews");
>      id  t_rightViews = object_getIvar(navigationBar, t_rightViewsm_mainFrameViewController_scrollViewivar);
>  ```
>
>  
```
cy# [[#0x183d6e00 valueForKey:@"m_delegate"] WCPayFacingReceiveChangeToFixedAmount]
```

>* sendActionsForControlEvents

```
    //    UIView  *normalRedEnvelopesButton  [self valueForKey:@"normalRedEnvelopesButton"];
//        [normalRedEnvelopesButton sendActionsForControlEvents:UIControlEventTouchUpInside];// OnMakeWCRedEnvelopesButtonClick
```
# III 、 other 

#### 3.0 变量的定义

> * var xx =
>
>   ```
>   var logic = [[NSClassFromString(@"CContactVerifyLogic") alloc] init];
>   ```
>
>   

#### 3.1 nextResponder 有助于分析控件的层级结构

#### 3.2 获取属性、 设置属性

>* _ivarDescription
>
>* [self valueForKey:@"m_delegate"]
>
>* 设置KVC属性
>
>  ```
>  cy# [logic setValue:@"hi" forKey:@"m_nsVerifyValue"];//设置object类型属性
>              [logic setValue:@2 forKey:@"m_uiOpCode"];//设置int类型属性
>  ```
>
>* class_getInstanceVariable
>```
>    Ivar inputToolViewIvar = class_getInstanceVariable(objc_getClass("BaseMsgContentViewController"), "_inputToolView");
>   id inputToolView = object_getIvar(self, inputToolViewIvar);
>```

#### 3.3 setHidden
>* 3.3.1 `验证是否找对了view`
```
 cy# [#0x189ce7e0 setHidden:0] 
```



#### 3.4 AssociatedObject

> * AssociatedObject
>
>   ```objc
>   //1、key 的三种方式
>   static const void *kAssociatedKey = &kAssociatedKey;// 使用kAssociatedKey 最为key
>   //2、static  void *kAssociatedKey;//将&kAssociatedKey 做为key
>   //3、@selector(associatedObject)
>   
>   objc_setAssociatedObject(myClass, kAssociatedKey, @"AssociatedObject1", OBJC_ASSOCIATION_RETAIN_NONATOMIC);    
>       NSString* associatedString = objc_getAssociatedObject(myClass, kAssociatedKey);   
>       NSLog(@"associatedString: %@", associatedString);
>   ```
>
>   ```
>   /**bool 类型 YES 不允许点击   NO 允许点击   设置是否执行点UI方法*/
>   @property (nonatomic, assign) BOOL isIgnoreEvent;
>   //runtime 动态绑定 属性
>   - (void)setIsIgnoreEvent:(BOOL)isIgnoreEvent{
>       // 注意BOOL类型 需要用OBJC_ASSOCIATION_RETAIN_NONATOMIC 不要用错，否则set方法会赋值出错
>       objc_setAssociatedObject(self, @selector(isIgnoreEvent), @(isIgnoreEvent), OBJC_ASSOCIATION_RETAIN_NONATOMIC);
>   }
>   - (BOOL)isIgnoreEvent{
>       //_cmd == @select(isIgnore); 和set方法里一致
>       return [objc_getAssociatedObject(self, _cmd) boolValue];
>   }
>   ```
>
>   ```
>   //结合@dynamic的 associatedObject例子
>   @implementation NSObject (AssociatedObject)
>   @dynamic associatedObject;
>   - (void)setAssociatedObject:(id)object {
>       objc_setAssociatedObject(self,
>   @selector(associatedObject), object,
>   OBJC_ASSOCIATION_RETAIN_NONATOMIC);
>   }
>   - (id)associatedObject {
>       return objc_getAssociatedObject(self,
>   @selector(associatedObject));
>   }
>   ```
>
>   
>
>   ```
>   @property (nonatomic, copy) NSString* newProperty;
>   %new
>   - (id)newProperty {
>       return objc_getAssociatedObject(self, @selector(newProperty));
>   }
>   
>   %new
>   - (void)setNewProperty:(id)value {
>       objc_setAssociatedObject(self, @selector(newProperty), value, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
>   }
>   ```
>
>   



# See Also 

> * [Method Swizzling](https://kunnan.github.io/2017/02/04/Objective-C_Runtime_Cases/)
>
>   ```objc
>   //可以用于日志记录和 Mock 测试。例如上报用户打开的界面所在VC的名称，就可以使用swizzling 统一处理
>   void Swizzle(Class c, SEL orig, SEL new) {
>       Method origMethod = class_getInstanceMethod(c, orig);
>       Method newMethod = class_getInstanceMethod(c, new);
>       if(class_addMethod(c, orig, method_getImplementation(newMethod), method_getTypeEncoding(newMethod)))//给一个方法添加新的方法和实现;Adds a new method to a class with a given name and implementation.
>           //若返回Yes说明类中没有该方法，然后再使用 `class_replaceMethod()` 方法进行取代
>           class_replaceMethod(c, new, method_getImplementation(origMethod), method_getTypeEncoding(origMethod));//取代了对于一个给定类的实现方法
>       else
>           //YES if the method was added successfully, otherwise NO (for example, the class already contains a method implementation with that name).
>           method_exchangeImplementations(origMethod, newMethod);//交换两个类的实现方法
>   }
>   
>   //当类加载之后，会调用一个名为 load 的类函数
>   + (void)load{//不会碰到并发问题。 
>       static dispatch_once_t onceToken;
>       dispatch_once(&onceToken, ^{//由于我们只打算混淆一次，因此我们需要使用 dispatch_once
>           Swizzle([UIViewController class], @selector(viewWillAppear:), @selector(autolog_viewWillAppear:));
>           Swizzle([UIViewController class], @selector(viewWillDisappear:), @selector(autolog_viewWillDisAppear:));
>           Swizzle([UIViewController class], @selector(presentViewController:animated:completion:), @selector(autoLog_presentViewController:animated:completion:));
>           
>           Swizzle([UIViewController class], @selector(dismissViewControllerAnimated:completion:), @selector(autoLog_dismissViewControllerAnimated:completion:));
>       });
>   }
>   
>   -(void)autolog_viewWillDisAppear:(BOOL)animated{
>       
>       if (![self isKindOfClass:[UINavigationController class]] && ![self isKindOfClass:[UITabBarController class]] && ![self isKindOfClass:[xxx class]] && ![self isKindOfClass:[xx class]]) {
>           [MobClick endLogPageView:NSStringFromClass([self class])];//友盟统计模块
>       }
>       [self autolog_viewWillDisAppear:animated];//看似会陷入递归调用，
>       //其实则不会，因为我们已经在`+ (void)load `方法中更换了`IMP`,他会调用`viewWillDisAppear:`方法，然后在后面添加我们需要添加的功能。
>   
>   }
>   
>   ```
>
>   

>* 微信资助二维码
>![image](https://wx4.sinaimg.cn/large/af39b376gy1fsm4fqug79j207s07s3yc.jpg)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost CycriptUsefulCommand 常用的cycript命令、分析思路 -t Cycript
> #原来""的参数，需要自己加上""
```

>* [CycriptTricks](https://kunnan.github.io/2018/04/20/CycriptTricks/)
>* [UIButton_sendActionsForControlEvents](https://kunnan.github.io/2018/06/08/UIButton_sendActionsForControlEvents/)
>* [csdn](https://blog.csdn.net/z929118967/article/details/78309400)


```

```

```

```