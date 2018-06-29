---
layout: post
title: alfredapp
date: 2018-06-29
tag: Efficiency
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Alfred is an award-winning app for Mac OS X which boosts your efficiency with hotkeys, keywords, text expansion and more. Search your Mac and the web, and be more productive with custom actions to control your Mac
---



# Workflow

任何微小的工作，都可以拆分成多个步骤，这些步骤顺序相连，依次进行，最终输出成果，有些步骤可能存在多个分支，并且最终输出多个成果。这些步骤依次执行，并且向后传递阶段性信息的流，就是工作流。

# 如何创建第一个 Workflow



> * 选择 Blank Workflow
>
>   ![image](https://wx4.sinaimg.cn/large/af39b376gy1fss07q4mdng206w04mmx9.gif)

> * 补全 Workflow 相关信息
>
>   

> * 搭建一个 Google 搜索的工作流，通过这个工作流，我们能快速的选中文本然后使用 Google 搜索该文本(完全不需要编程基础)
>
>   ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss0gea0zhg20h60bgtda.gif)

# Workflow 支持什么功能

 

#### Workflow 支持` Triggers、Inputs、Actions、Utilities（Alfred 3.x 新增）、Outputs `共 5 项主要功能

![image](https://wx1.sinaimg.cn/large/af39b376gy1fss0iwf63hj204k076dgb.jpg)



#### 5 项功能一共包含 39 个组件

> * `输入`包含 `Triggers`（触发器）和 `Inputs（`输入触发）；`Triggers` 中的流程可以触发 `Inputs `的流程，反之则不行，同时它们都可以触发其它后续流程。
> * `输出`即 Outputs，包含了`通知，放大展示、复制到剪切板，写入文本、播放声音、触发其它流程`等。
> * 中间` Actions `包含`打开文件、在 finder 中展示文件、唤起 app、打开 web search、打开 URL、执行系统命令、执行 iTunes 命令、执行脚本、执行 AppleScript 脚本、在终端中执行命令`等。
> * `Utilities` 包含了一些公共组件，如`变量设置、json 配置、过滤、转换、替换、延时、debug` 等。
>
> 
>
> 
>
>  
>
> 

> * `Hotkey、Keyword、Script Filter `是常用的`输入组件`，`Open URL、Run Script` 是高频的 Action 组件，`Post Notification、Copy to Clipboard `是受欢迎的输出组件，而` Arg and Vars、Filter、Delay、Debug` 是贴心的公共组件

#  Workflow 内置了多种语言支持。

> * 我们不需要关心语言之间的交互细节，只需要使用它们接收输入，提供输出，其它事情统统交给 Alfred
>
>   

> * 8 种语言编写脚本:
>
>   - Bash
>   - zsh
>   - PHP
>   - Ruby
>   - Python( Alfred Workflow 中最常用的编程语言)
>   - Perl
>   - AppleScript
>   - JavaScript
>
>    
>
>   
>
>   

> * 演示:新增流程去打开 iTerm 并执行 `ll` 命令的过程
>
>   ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss0r02mxog20h707qdj0.gif)
>
>   



# See Also 

>* [开发效率提升：Mac生产力工具链推荐](https://github.com/kunnan/tool)
>* [效率神器alfred的workflow分享](https://github.com/Louiszhai/alfred-workflows)
>
>* [Alfred Workflows 收集表格www.alfredworkflow.com](http://www.alfredworkflow.com/)
>* [Alfred 神器使用手册](https://sspai.com/post/44624)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost alfredapp Alfred is an award-winning app for Mac OS X which boosts your efficiency with hotkeys, keywords, text expansion and more. Search your Mac and the web, and be more productive with custom actions to control your Mac -t Efficiency
> #原来""的参数，需要自己加上""
```

