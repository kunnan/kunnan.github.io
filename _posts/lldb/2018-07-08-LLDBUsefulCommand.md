---
layout: post
title: LLDBUsefulCommand
date: 2018-07-08
tag: lldb
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: lldb 调试常用的命令
---



# 动态跟踪方法

> * `(lldb) bclass TipsView `
>
>   ```
>   (lldb) br command add 1
>   Enter your debugger command(s).  Type 'DONE' to end.
>   > po $x0
>   > po $x1
>   > po $x2
>   > po $x3
>   > po $x4
>   > po $x5
>   > po $x6
>   > c
>   > DONE
>   ```
>
>   ![image-20180712174907879](/var/folders/8s/t119mw8d4lsdztx8h9q8113m0000gn/T/abnerworks.Typora/image-20180712174907879.png)
>
>   ![image](https://ws2.sinaimg.cn/large/af39b376ly1ft781l8zm1j20u01hcu0y.jpg)





# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost LLDBUsefulCommand lldb 调试常用的命令 -t lldb
> #原来""的参数，需要自己加上""
```

