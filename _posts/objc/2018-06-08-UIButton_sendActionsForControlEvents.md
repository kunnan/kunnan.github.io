---
layout: post
title: UIButton_sendActionsForControlEvents
date: 2018-06-08
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: iOS 代码触发button点击事件
---



#  I、在逆向分析中的应用


#### 1.1 步骤1: 利用cy 找到对应的`按钮地址`

>*  `[UIWindow keyWindow].recursiveDescription().toString()` 展开views结构
```
[UIWindow keyWindow].recursiveDescription().toString()
```

>* [按钮文字unicode转码](http://tool.chinaz.com/tools/unicode.aspx)

```
\u4e8c\u7ef4\u7801\u6536\u6b3e 进行搜索找到对应按钮
```

```
   |    |    |    |    |    |    |    |    |    |    | <WCPayOfflinePayBottomButton: 0x170bab80; baseClass = 
```


#### 1.2  步骤2：寻找 `点击按钮对应的执行方法`

 UIButton 是继承 UIControl 的，而 UIControl 的话可以通过allTargets 与 allControlEvents查看所有的对象与事件，再使用actionsForTarget:forControlEvent:从而找到触发的方法
 
```
- (nullable NSArray<NSString *> *)actionsForTarget:(nullable id)target forControlEvent:(UIControlEvents)controlEvent;    // single event. returns NSArray of NSString selector names. returns nil if none
```
>* allTargets

```
cy# [#0x17a9cad0 allTargets]
[NSSet setWithArray:@[#"<WeixinContactInfoAssist: 0x174eb8b0>"]]]
```

>* allControlEvents

```
cy# [#0x17a9cad0 allControlEvents]
64
```

>* actionsForTarget: forControlEvent

```
cy# [#0x17a9cad0 actionsForTarget:#0x174eb8b0  forControlEvent:64]
@["onVerifyContactOk"]
```

>* 最终确定：`WeixinContactInfoAssist WeixinContactInfoAssist:`

```
- (void)onVerifyContactOk;
- 
```

#### 1.3 小结

>* 获取allTargets
```
cy# [[#0x170bab80 allTargets] allObjects][0]
#"<WCPayOfflinePayViewController: 0x1642b200>"
```
>* 获取 actions
```
cy# [#0x170bab80 actionsForTarget:[[#0x170bab80 allTargets] allObjects][0] forControlEvent:[#0x170bab80 allControlEvents]]
@["receiveMoneyBtnPress:"]
```

>* cy# [#0x189ce7e0 setHidden:0] `验证是否找对了view`

###### 1.3.1  进行验证方法

>* objc_msgSend
```
            objc_msgSend(vc, @selector(initView));
```
>* []
```
cy# [[[#0x170bab80 allTargets] allObjects][0] receiveMoneyBtnPress:nil]
```
>* valueForKey
```
cy# [[#0x183d6e00 valueForKey:@"m_delegate"] WCPayFacingReceiveChangeToFixedAmount]
```


#### 1.4 [相关code](https://github.com/saulTong/WeChatTools/blob/a3df784477898cd181d5a86c37ad9255640dfc1f/WeChat/branches/saulremovefriend/6.1.5/saulremovefriend/Tweak.xm)


######  1.4.1  ContactInfoViewController

>* cy# [#0x16b60600 _ivarDescription].toString()

```
 CBaseContactInfoAssist *m_oContactInfoAssist;
     CContact *m_contact;
\tm_uiVerify (unsigned long): <01000000>
\tm_contact (CContact*): <CPushContact: 0x177450d0>
\tm_oContactInfoAssist (CBaseContactInfoAssist*): <WeixinContactInfoAssist: 0x174eb8b0>     
```
>* 书写 `tweak`
```
%hook ContactInfoViewController
- (void)viewWillAppear:(BOOL)arg1 {
%orig;

            Ivar mDelegateVar = class_getInstanceVariable(objc_getClass("ContactInfoViewController"), "m_oContactInfoAssist");
            WeixinContactInfoAssist * mDelegate = object_getIvar(self, mDelegateVar);
            [mDelegate onAction];
        Ivar mDelegateVar1 = class_getInstanceVariable(objc_getClass("ContactInfoViewController"), "m_contact");
            
}
```

# II、基础知识、概念

##### 2.1  UIControl

```
NS_CLASS_AVAILABLE_IOS(2_0) @interface UIControl : UIView

// send the action. the first method is called for the event and is a point at which you can observe or override behavior. it is called repeately by the second.
- (void)sendAction:(SEL)action to:(nullable id)target forEvent:(nullable UIEvent *)event;
- (void)sendActionsForControlEvents:(UIControlEvents)controlEvents;                        // send all actions associated with events
```


#### 2.2 UIButton

```
NS_CLASS_AVAILABLE_IOS(2_0) @interface UIButton : UIControl <NSCoding>
```



#### 2.3 iOS 代码触发button点击事件

```
cy# [#0x18c0ad10 sendActionsForControlEvents:UIControlEventTouchUpInside]
```
```
[btn sendActionsForControlEvents:UIControlEventTouchUpInside];
```

 
# III、See Also 


>* [WechatExport](https://github.com/stomakun/WechatExport-iOS)
>* [UIButton_sendActionsForControlEvents](https://blog.csdn.net/z929118967/article/details/80782694)

