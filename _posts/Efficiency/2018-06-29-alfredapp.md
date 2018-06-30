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

# 前言

####  [ `alfred`](https://github.com/idetool/Alfred3Powerpack/blob/master/14334408_Alfred%20Powerpack%203.6.903%20%5BCORE%5D.dmg)**彻底解决了输入输出的痛点，极大的减少了程序之间的切换成本和重复按键成本**;快速打开软件、自定义脚本执行

> * 之前觉得Spotlight Search很牛逼，现在发现更好用
> * ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss24h6i5uj20ja01sq34.jpg)

> * `Spotlight Search`的默认唤起快捷键：control+space
> * 切换输入法的快捷键：command+space
> * 打开Alfred的默认快捷键：option+space



# I、alfred能做什么？



> * 默认情况下，alfred 至少能胜任 15 项工作：
>
>   1. 应用搜索
>
>   2. 文件或目录搜索
>
>   3. 文本内容搜索
>
>   4. 标记搜索
>
>   5. 快捷网页搜索
>
>   6. 书签搜索
>
>   7. 计算器
>
>   8. 词典搜索
>
>   9. 通讯录搜索
>
>   10. 剪切板搜索
>
>   11. 代码片段搜索
>
>   12. iTunes管理
>
>   13. 1Password搜索
>
>   14. 系统常用命令快捷操作
>
>   15. 直接唤起指定终端并执行命令
>
>   16. 获得 powerpack license 的 alfred 将获得强大的 workflows 功能，后续将专门讲解。( 付费版本、破解版本)
>
>       

 

# II、file search 

>* alfred preferences 面板：（`Cmd+,`唤起）—> features 栏—> file search 功能
>* ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss4d3lks7j21ba0u2q9v.jpg) 


#### 2.1.应用搜索

![image](https://wx1.sinaimg.cn/large/af39b376gy1fss38arujaj20k3086dhh.jpg) 







#### 2.2 文件或目录搜索

 输入 find 或 open 命令，以及待搜索的文件或目录名，列出磁盘中的相关文件，可以快速定位 finder

![image](https://wx1.sinaimg.cn/large/af39b376gy1fss3b5sqw4j20ie08hdhn.jpg)

#### 2.3. 文本内容搜索

 输入 in 命令，以及待搜索的文本

![image](https://wx1.sinaimg.cn/large/af39b376gy1fss3ao1mbqj20je09ldi5.jpg)



#### 2.4. 标记搜索

 输入 tags 命令，以及待搜索的标记颜色中文名称，列出打上相应标记的目录，可以快速定位标记目录

![image](https://wx1.sinaimg.cn/large/af39b376gy1fss3cc3t1hj20jq075tag.jpg)





# III、 快捷网页搜索




> * 输入关键字如『wiki』空格后再输入搜索内容，最后再回车打开 wiki 网站，如下所示：
> ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss49fuqzgj20ho05k74w.jpg)


> * [『Add Custom Search』按钮新增你常用的网页搜索:](alfred://customsearch/github%20%7Bquery%7D/github/utf8/nospace/https%3A%2F%2Fgithub.com%2Fsearch%3Futf8%3D%25E2%259C%2593%26q%3D%7Bquery%7D%26type%3D)
>
> * ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss3s71py8j20ui0i9tho.jpg)
>
>   ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss432uwbmj210c0l4tdj.jpg)
>   ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss4a0fp14j20if05gmxt.jpg)



# IV、书签搜索

>* 书签搜索是 alfred3.x 版本中新加的功能，方便用户在浏览器的大量书签中快速搜索
>![image](https://wx1.sinaimg.cn/large/af39b376gy1fss51vysdpj20i50hkdke.jpg)
>



# V 、Calculator

>* ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss54175pdj20ic05laay.jpg)
>



# VI、Dictionnary
>* ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss57gnyp5j20ia0aqtb5.jpg)



# VII 、search contanct
>* ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss599hdc5j20i20aiwgl.jpg)
>



# VIII、System 

> * 系统常用命令快捷键
> * `清空垃圾篓emptytrash`、`ejectall`等指令可能较为常用
> * ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss6duldioj218w0vkk08.jpg)
> * ![image](https://wx1.sinaimg.cn/large/af39b376gy1fss6e2j2gtj20uk0ian4r.jpg)
> * [Qrcode URL生成二维码，如果网页中包含选中文本，则生成选中文本的二维码](https://chrome.google.com/webstore/detail/qrcode/cmpjmgpafdgofigbhbneckneoakpdhag?utm_source=chrome-ntp-icon)




# IX、 alfred 设置界面的10个面板

#### 9.0、从I-VIII 的章节，主要说明Features 面板的功能；9个面板如下所示：

> * General（通用，用于设置是否开机启动，及设置唤起快捷键，通常设置为 `Alt+Space` 即可）
> * Workflows（工作流）
> * Appearance（外观，用于设置 alfred 输入窗口的外观、字体、颜色等）
> * Advanced（高级）
> * Remote（远程，用于远程管理，这意味着你需要在 App Store 购买一个 Alfred Remote 的app，然后便可以在手机上远程操作 mac）
> * Powerpack（许可证，购买 powerpack 的用户便可以使用 workflow 功能）
> * Usage（使用统计）
> * Help（帮助，提供快速上手文档、使用文档、反馈bug、用户论坛等链接）
> * Update（更新日志，可查看更新日志及更新到最新版）
>
>  

#### 9.1 Appearance



![image](https://ws4.sinaimg.cn/large/af39b376gy1fss6ohc7bij21p210gb1u.jpg)





# [Workflow](https://www.alfredapp.com/help/workflows/)

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

####  破解版资源

> * [alfred361 for mac](http://www.sdifen.com/?s=alfred&submit=%E6%90%9C%E7%B4%A2)
>
> * [Alfred3Powerpack:V3.6.903](https://github.com/idetool/Alfred3Powerpack)
>
>   
>
>   

#### workflow

> * [workflow](http://www.packal.org/workflow/chrome-bookmarks-0)
>
> * [有心网友甚至罗列了一张表格来管理它，表格的每一行都解锁了一项](http://www.alfredworkflow.com/\)(注意并非所有的 workflow 都支持最新的 alfred 3.6.1版本）
>
> * [ Search Workflows...](http://www.packal.org/)
>* [效率神器alfred的workflow分享](https://github.com/Louiszhai/alfred-workflows)
>   
>
>   
>

#### other

>* [开发效率提升：Mac生产力工具链推荐](https://github.com/kunnan/tool)
>* [alfred能做什么？](http://louiszhai.github.io/2018/05/31/alfred/#%E5%A6%82%E4%BD%95%E5%88%9B%E5%BB%BA%E7%AC%AC%E4%B8%80%E4%B8%AAworkflow)
>* [Alfred Workflows 收集表格www.alfredworkflow.com](http://www.alfredworkflow.com/)
>* [Alfred 神器使用手册](https://sspai.com/post/44624)
>* [alfred3 for mac 破解版](http://www.sdifen.com/alfred3.html)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost)  
```
/Users/devzkn/bin//knpost alfredapp Alfred is an award-winning app for Mac OS X which boosts your efficiency with hotkeys, keywords, text expansion and more. Search your Mac and the web, and be more productive with custom actions to control your Mac -t Efficiency
> #原来""的参数，需要自己加上""
```

