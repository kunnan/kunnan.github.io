---
layout: post
title: Fixed-point_Zoom_pop-up_window
date: 2018-12-15
tag: iOS
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: iOS开发中常用的动画（定点缩放弹窗)
---



# 前言

每一个UIView内部都默认关联着一个CALayer, UIView有frame、bounds和center三个属性，CALayer也有类似的属性，分别为frame、bounds、position、anchorPoint



# CALayer

####  anchorPoint

* anchorPoint就相当于白纸上的图钉，它主要的作用就是用来作为变换的支点，旋转就是一种变换，类似的还有平移、缩放。




# iOS开发中常用的动画（定点缩放弹窗）



```objc
/**
 1、点击弹出按钮时，阴影alpha由0到1，弹窗的scale由0到1（这里使用CABasicAnimation）
2、 点击空白处，再让阴影alpha由1到0，弹窗的scale由1到0（同样使用CABasicAnimation），动画完成后移除阴影和弹窗
 */
- (void)expandView{
    //展示的时候，动画从右上角往左下脚延伸；隐藏的时候，动画从左下脚往右上角收回
    [MemberCardMenuView setAnchorPoint:CGPointMake(0.9f, 0.0f) forView:self];

    self.transform = CGAffineTransformMakeScale(0.001f, 0.001f);

    
    self.hidden = NO;// 修改为动画， MemberCardMenuView 提供一个动画的实例方法
    
    self.cover.hidden = NO;
    
    self.cover.alpha = 0;
    
    [UIView animateWithDuration:0.3 animations:^{
        self.transform = CGAffineTransformMakeScale(1.f, 1.f);
        self.cover.alpha = 1;
        
    } completion:^(BOOL finished) {
        self.transform = CGAffineTransformIdentity;
        
        


    }];

    
}

- (void)foldView{
    
    /*
     （0，0） 为左上角，（0，1） 为左下角，
     （1， 0）右上， （1，1） 右下
     */
    
    [UIView animateWithDuration:0.3 animations:^{
        
        self.transform = CGAffineTransformMakeScale(0.001f, 0.001f);


        self.cover.alpha = 0;
        
    } completion:^(BOOL finished) {
        self.hidden = YES;
        self.cover.hidden = YES;
        self.transform = CGAffineTransformIdentity;        
    }];

}


```

* 修改anchorPoint而不想移动layer，在修改anchorPoint后再重新设置一遍frame就可以达到目的，这时position就会自动进行相应的改变

```objc
+ (void) setAnchorPoint:(CGPoint)anchorpoint forView:(UIView *)view{
    CGRect oldFrame = view.frame;
    view.layer.anchorPoint = anchorpoint;
    view.frame = oldFrame;
}
```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Fixed-point_Zoom_pop-up_window iOS开发中常用的动画（定点缩放弹窗) -t iOS
> #原来""的参数，需要自己加上""
```

