---
layout: post
title: Network_Layer
date: 2018-05-24
tag: internet
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 揭秘层与层之间的关系
---



# 网络为什么要分层？


>* 复杂的程序都要分层，这是程序设计的要求。
比如，复杂的电商还会分数`据库层、缓存层、Compose 层、Controller 层和接入层`，每一层专注做本层的事情

# 程序是如何工作的？

![image](https://wx4.sinaimg.cn/large/006tBeITgy1frmgpgx22fj30zs0zkdke.jpg)

# 揭秘层与层之间的关系


只要是在网络上跑的包，都是完整的。可以有下层没上层，绝对不可能有上层没下层


>* 一个 HTTP 协议的包经过一个二层设备，二层设备收进去的是整个网络包。这里面 HTTP、TCP、 IP、 MAC 都有。
>```
>1) 什么叫二层设备呀，就是只把 MAC 头摘下来，看看到底是丢弃、转发，还是自己留着。
>2) 那什么叫三层设备呢？就是把 MAC 头摘下来之后，再把 IP 头摘下来，看看到底是丢弃、转发，还是自己留着。
>```


# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Network_Layer 揭秘层与层之间的关系 -t internet
> #原来""的参数，需要自己加上""
```

