---
layout: post
title: SpringBoard
date: 2018-07-17
tag: SpringBoard
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 分析SpringBoard的一些类和方法
---



# interface 



> * interface SpringBoard 
>
>   
>
>   ```
>   /*
>    @interface SpringBoard : UIApplication
>    \t_uiController (SBUIController*): <SBUIController: 0x1809c510>
>    - (void)relaunchSpringBoard;  [#0x1617ca00 relaunchSpringBoard]
>    - (void)_relaunchSpringBoardNow;
>    - (void)powerDown;
>    - (void)_powerDownNow;
>    - (void)reboot;
>    - (void)_rebootNow;
>    @end
>    */
>   ```
>
>   * sharedApplication 
>
>     ```
>     [SpringBoard sharedApplication]
>     #"<SpringBoard: 0x180e2000>"
>     ```
>
>     * keyWindow 
>
>       ```
>       // 有弹框的时候是_UIAlertControllerShimPresenterWindow
>       cy# [SpringBoard sharedApplication].keyWindow
>       #"<_UIAlertControllerShimPresenterWindow: 0x18cc3e20; frame = (0 0; 320 568); opaque = NO; gestureRecognizers = <NSArray: 0x18e58d60>; layer = <UIWindowLayer: 0x18c27270>>"
>       // 没有弹框的时候是SBAppWindow
>       cy# [SpringBoard sharedApplication].keyWindow
>       #"<SBAppWindow: 0x1889eb60; baseClass = UIWindow; frame = (0 0; 320 568); opaque = NO; gestureRecognizers = <NSArray: 0x1889f4a0>; layer = <UIWindowLayer: 0x1889ed30>>"
>       ```
>
>       



#### SpringBoard 

> * _ivarDescription 
>
>   ```
>   cy# [#0x173d8800 _ivarDescription].toString()
>   `<SpringBoard: 0x173d8800>:
>   in SpringBoard:
>   \t_uiController (SBUIController*): <SBUIController: 0x1809c510>
>   ```
>
>   



#### SBUIController 

> * _ivarDescription 
>
>   ```
>   cy# [#0x1809c510 _ivarDescription].toString()
>   `<SBUIController: 0x1809c510>:
>   in SBUIController:
>   \t_window (SBWindow*): <SBAppWindow: 0x180bd700>
>   \t_iconsView (UIView*): <SBIconContentView: 0x16fb82d0>
>   \t_contentView (UIView*): <UIView: 0x16fb69c0>
>   \t_fakeSpringBoardStatusBar (UIStatusBar*): <SBFakeStatusBarView: 0x175b0e00>
>   \t_switcherController (SBAppSwitcherController*): <SBAppSwitcherController: 0x17452a00>
>   \t_switcherWindowController (SBAppSwitcherWindowController*): <SBAppSwitcherWindowController: 0x16f18d20>
>   ```
>
>   

#### SBAppWindow 

> * _ivarDescription 
>
>   ```
>   `<SBAppWindow: 0x1889eb60>:
>   in SBAppWindow:
>   in SBWindow:
>   \t_recycledViewsContainerView (SBRecycledViewsContainer*): <SBRecycledViewsContainer: 0x18bd6ca0>
>   \t_jailBehavior (int): 0
>   \t_layoutStrategy (<SBWindowLayoutStrategy>*): <SBDefaultWindowLayoutStrategy: 0x18866a90>
>   in FBWindow:
>   in UIWindow:
>   \t_delegate (id): nil
>   \t_windowLevel (float): -1
>   ```
>
>   



# 常用的一些API



#### SpringBoard  相关

> * powerDown 
>
>   ```
>   + (void) powerDown {
>       id SpringBoard = [UIApplication sharedApplication];//#"<SpringBoard: 0x173d8800>"
>       [SpringBoard powerDown];
>   }
>   ```
>
>   * relaunchsb
>
>     ```
>      @interface SpringBoard : UIApplication
>      \t_uiController (SBUIController*): <SBUIController: 0x1809c510>
>      - (void)relaunchSpringBoard;  [#0x1617ca00 relaunchSpringBoard]
>      - (void)_relaunchSpringBoardNow;
>      - (void)powerDown;
>      - (void)_powerDownNow;
>      - (void)reboot;
>      - (void)_rebootNow;
>      @end
>     ```
>
>     

#### UIApplication  相关

> * idleTimerDisabled 
>
>   ```
>       [UIApplication sharedApplication].idleTimerDisabled=YES;//不自动锁屏,放在-(void)viewWillAppear:(BOOL)animated里面的时候，防止失效
>     //
>       //[UIApplication sharedApplication].idleTimerDisabled=NO;//自动锁屏
>   ```
>
>   

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost SpringBoard 分析SpringBoard的一些类和方法 -t SpringBoard
> #原来""的参数，需要自己加上""
```

