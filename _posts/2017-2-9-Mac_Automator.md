---
layout:     post
title:      Mac_Automator
subtitle:   在Mac下为终端设置快捷键,创建工作流，调试AppleScript
date:       2017-02-06
author:     BY
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - Mac
    - 效率
    - 开发技巧
---

# 前言

>在Mac下快速调出终端的方法是：采用Automator创建服务，为服务创建快捷键。

# 为终端添加一个快捷键打开方式

#### 打开Mac下自带的软件 **Automator**


采用AppleScript创建一个服务：


修改框内的脚本

```
on run {input, parameters}
	tell application "Terminal"
		reopen
		activate
	end tell
end run

```
运行：`command + R`，进行运行脚本，调试代码。

保存：`Command + S`，将新创建的服务命名为`reopenTerminal`

#### 设置快捷键

```
在 **系统偏好设置** -> **键盘设置** -> **快捷键** -> **服务**

选择创建好的对应服务，设置快捷键。我设置的是`command+T`,符合我的习惯。
```

![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqk1u7u2rcj30ha083dhf.jpg)


## 黑技能


创建一个`Shell`脚本，

勾选:`用输出内容替换所选文本`

输入：`sort|uniq` 

