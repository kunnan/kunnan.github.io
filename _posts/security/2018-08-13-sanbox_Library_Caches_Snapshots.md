---
layout: post
title: sanbox_Library_Caches_Snapshots
date: 2018-08-13
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 应用进入后台时的截屏行为,防止手机截屏图片泄密方案：
---



# 前言

![image](https://ws1.sinaimg.cn/large/af39b376gy1fu7w0lma7vj20vm04qgn0.jpg)

> * 当应用进入后台时，系统会自动在当前应用的页面截屏并存储到手机内，如果当前页面涉及敏感信息时，被攻击会造成泄密。如下图生成的两张图片路径：
>
>   * /var/mobile/Containers/Data/Application/2BEE545F-A6E7-4473-97C2-D192A0BE360C/Library/Caches/Snapshots/com.tencent.xin 
>
>     * downscaled 
>
>       
>
>       ```
>       iPhone:/var/mobile/Containers/Data/Application/2BEE545F-A6E7-4473-97C2-D192A0BE360C/Library/Caches/Snapshots/com.tencent.xin root# ls -lrt downscaled
>       total 120
>       -rw-r--r-- 1 mobile mobile 119370 Aug 13 10:28 03C8F347-0B56-484C-83DF-B4574CA877C2@2x.ktx
>       
>       ```
>
>       
>
>     ```
>     iPhone:/var/mobile/Containers/Data/Application/2BEE545F-A6E7-4473-97C2-D192A0BE360C/Library/Caches/Snapshots/com.tencent.xin root# ls -rlt
>     total 640
>     -rw-r--r-- 1 mobile mobile 256173 Aug 13 09:33 389A646A-4A29-461C-A184-F198BD185545@2x.ktx
>     -rw-r--r-- 1 mobile mobile 247577 Aug 13 09:33 121427F4-B7DE-4AB0-8E12-79AF1BA86E35@2x.ktx
>     drwxr-xr-x 2 mobile mobile    102 Aug 13 10:28 downscaled
>     -rw-r--r-- 1 mobile mobile 144433 Aug 13 10:28 9F042B65-6ED4-4BEB-80A2-DF3A321589D1@2x.ktx
>     
>     ```
>
>     

 ![image](https://ws1.sinaimg.cn/large/af39b376gy1fu7w44sw2bj20ih040q3b.jpg)





# **防止手机截屏图片泄密方案：**添加一层高斯模糊

 

> * 一般我们在应用被挂起时，在当前页面添加一层高斯模糊，在应用重新进入前台时，删除模糊效果，代码如下
>
>   * 将UIToolbar 视图从 windo移除
>
>     ![image](https://ws1.sinaimg.cn/large/af39b376gy1fu7w95n2qrj20td044q42.jpg)
>
>   *  往window添加UIToolbar 视图
>
>     ![image](https://ws1.sinaimg.cn/large/af39b376gy1fu7w7vgs33j20td05tq4s.jpg)

 

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost sanbox_Library_Caches_Snapshots 应用进入后台时的截屏行为,防止手机截屏图片泄密方案： -t security
> #原来""的参数，需要自己加上""
```

