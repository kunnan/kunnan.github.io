---
layout: post
title: DerekSelander_LLDB
date: 2018-07-07
tag: Debug
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: A collection of LLDB aliases/regexes and Python scripts to aid in your debugging sessions
---

# 前言



lldb 会默认从`~/.lldbinit `加载自定义脚本。新增command script之后，重启Xcode，或者直接在lldb交互模式下输入`command source ~/.lldbinit`来加载脚本。

# Installation

> * git clone 
>
>   ```
>   ➜  lldb git clone git@github.com:DerekSelander/LLDB.git
>   ```
>
>   ```
>   1、To Install, copy the lldb_commands folder to a dir of your choosing.
>   2、Open up (or create) ~/.lldbinit
>   3、Add the following command to your ~/.lldbinit file: command script import /path/to/lldb_commands/dslldb.py
>   ```
>
>   



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost DerekSelander_LLDB A collection of LLDB aliases/regexes and Python scripts to aid in your debugging sessions -t Debug
> #原来""的参数，需要自己加上""
```

