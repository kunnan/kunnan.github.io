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


# UIControl

```
NS_CLASS_AVAILABLE_IOS(2_0) @interface UIControl : UIView

// send the action. the first method is called for the event and is a point at which you can observe or override behavior. it is called repeately by the second.
- (void)sendAction:(SEL)action to:(nullable id)target forEvent:(nullable UIEvent *)event;
- (void)sendActionsForControlEvents:(UIControlEvents)controlEvents;                        // send all actions associated with events
```


# UIButton

```
NS_CLASS_AVAILABLE_IOS(2_0) @interface UIButton : UIControl <NSCoding>
```



# iOS 代码触发button点击事件

```
cy# [#0x18c0ad10 sendActionsForControlEvents:UIControlEventTouchUpInside]
```
```
[btn sendActionsForControlEvents:UIControlEventTouchUpInside];
```

#  在逆向分析中的应用


#### 利用cy 找到对应的按钮

```
[UIWindow keyWindow].recursiveDescription().toString()
```

#### 利用UIControl的方法 定位

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


#### [相关code](https://github.com/saulTong/WeChatTools/blob/a3df784477898cd181d5a86c37ad9255640dfc1f/WeChat/branches/saulremovefriend/6.1.5/saulremovefriend/Tweak.xm)


######    ContactInfoViewController

>* cy# [#0x16b60600 _ivarDescription].toString()

```
 CBaseContactInfoAssist *m_oContactInfoAssist;
     CContact *m_contact;
\tm_uiVerify (unsigned long): <01000000>
\tm_contact (CContact*): <CPushContact: 0x177450d0>
\tm_oContactInfoAssist (CBaseContactInfoAssist*): <WeixinContactInfoAssist: 0x174eb8b0>     
```

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
 
# See Also 

>* [WechatExport](https://github.com/stomakun/WechatExport-iOS)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost UIButton_sendActionsForControlEvents iOS 代码触发button点击事件 -t objc
> #原来""的参数，需要自己加上""
```

