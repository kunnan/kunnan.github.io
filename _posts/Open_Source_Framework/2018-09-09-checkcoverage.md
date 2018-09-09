---
layout: post
title: checkcoverage
date: 2018-09-09
tag: Mac
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 查看保障状态
---



# [查看保障状态](https://checkcoverage.apple.com/cn/zh/?sn=)

> * 平常要注意Mac的电池保养
>   *  不用的时候要关机、拔掉电源，防止电池和供电模块摔坏，注意环境不要太潮湿

> * 只要符合sleep(min)>=displaysleep(min)>=disksleep就可以了，这样mac就可以正常休眠了。

> displaysleep
>  Mac闲置多长时间后进入显示器睡眠，单位是分钟，这个时间不能长于sleep下设置的时间
>  sleep
>  Mac闲置多长时间后进入睡眠，这个系统偏好设置里也有，单位是分钟
>  disksleep
>  Mac闲置多长时间后关闭硬盘。这个系统偏好里也有，只不过换了一个字眼—如果可能，使硬盘进入睡眠—勾上这个的话系统就会自动根据sleep的时间设一个合适的时间。单位是秒，这个时间不能长于sleep下设置的时间



# Mac 和节能相关的设置



> * BSD General Commands Manual 
>
>   * 
>   *  pmset -g custom
>     * 检查一下电源参数
>   * manipulate power management settings
>     * displaysleep - display sleep timer; replaces 'dim' argument in 10.4 (value in minutes, or 0 to disable)
>     * disksleep - disk spindown timer; replaces 'spindown' argument in 10.4 (value in minutes, or 0 to disable)
>     * sleep - system sleep timer (value in minutes, or 0 to disable)
>
> * 快捷键
>
>   - Ctrl+Shift+Power: 关闭屏幕
>   - Cmd+Opt+Power: 睡眠 (sleep)
>   - Cmd+Ctrl+Power: 重启 (restart)
>   - Cmd+Ctrl+Opt+Power: 关机 (shutdown)
>



#### 设置displaysleep

> * 系统偏好设置 – Mission Control – 触发角->HotCorners

![image](https://wx1.sinaimg.cn/large/af39b376gy1fv39vixmv3j20i60f440i.jpg)





#### 查看是否有进程 阻止休眠

![image](https://wx1.sinaimg.cn/large/af39b376gy1fv3dea1swtj20pf0dftbg.jpg)



# See Also 

>* [查找 Mac 的机型名称和序列号](https://support.apple.com/zh-cn/HT201581)
>  * 从屏幕左上角的苹果菜单 () 中选取“关于本机”。“关于本机”会显示 Mac 的概要信息，包括操作系统的名称和版本、机型名称和序列号：
>* [使用 Mac 上的“节能器”设置](https://support.apple.com/zh-cn/HT202824)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost checkcoverage 查看保障状态 -t Open_Source_Framework
> #原来""的参数，需要自己加上""
```

