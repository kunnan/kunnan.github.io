---
layout: post
title: deviceconsole
date: 2018-06-30
tag: Efficiency
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: An iOS system log tailer that doesn't suck. Improved
---



# 前言

 自从iOS8之后，我就习惯使用Mac系统自带的console ，后来发现有些同事的Mac中console 版本低，没有device 选项；于是乎，就想起了推荐他们使用[deviceconsole](https://github.com/kunnan/deviceconsole)



# 原版的[deviceconsole](https://github.com/rpetrich/deviceconsole)

> > *  deviceconsole --help
>
> ```
> ➜  bin git:(master) ✗ deviceconsole --help
> Usage: deviceconsole [options]
> Options:
>  -d      Include connect/disconnect messages in standard out
>  -u <udid>    Show only logs from a specific device
>  -p <process name>  Show only logs from a specific process
> 
> Control-C to disconnect
> Mail bug reports and suggestions to <ryan.petrich@medialets.com>
> ```
>
> 

# [改进版的 deviceconsole ](https://github.com/MegaCookie/deviceconsole)

> * knlog -help 
>
>   ```
>   Usage: knlog [options]
>   Options:
>   -i | --case-insensitive     Make filters case-insensitive
>   -f | --filter <string>      Filter include by single word occurrences (case-sensitive)
>   -x | --exclude <string>     Filter exclude by single word occurrences (case-sensitive)
>   -p | --process <string>     Filter by process name (case-sensitive)
>   -u | --udid <udid>          Show only logs from a specific device
>   -s | --simulator <version>  Show logs from iOS Simulator
>        --debug                Include connect/disconnect messages in standard out
>        --use-separators       Skip a line between each line
>        --force-color          Force colored text
>        --message-only          Display only level and message
>   Control-C to disconnect
>   ```
>
>   

# 编译之后的可执行文件

> * [knlog](https://github.com/zhangkn/KNBin/blob/master/knlog)

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>* [A cross-platform protocol library to communicate with iOS devices: 提供了很多与iOS交互的工具，例如端口映射查看log](https://github.com/libimobiledevice/libimobiledevice)
>* [Inter-processCommunicationByRrocketbootstrap](https://zhangkn.github.io/2018/01/Inter-processCommunicationByRrocketbootstrap/)
>* 
>
```
/Users/devzkn/bin//knpost deviceconsole An iOS system log tailer that doesn't suck. Improved -t Efficiency
> #原来""的参数，需要自己加上""
```

