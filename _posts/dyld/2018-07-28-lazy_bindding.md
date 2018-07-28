---
layout: post
title: lazy_bindding
date: 2018-07-28
tag: dyld
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 懒加载和非懒加载
---

# 前言

iOS为了加快启动速度，将符号分为懒加载和非懒加载符号

> * 非懒加载符号在dyld加载的时候就会绑定真实的地址值
>
> * 懒加载符号，只有第一次去调用它的时候，才会绑定真实的地址，在第二次调用时候使用真实的地址。
>
>   ![image](https://ws1.sinaimg.cn/large/af39b376gy1ftpergmzo4j20si0hy1kx.jpg)

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost lazy_bindding 懒加载和非懒加载 -t dyld
> #原来""的参数，需要自己加上""
```

