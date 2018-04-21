---
layout:     post
title:      Mac_Automator
subtitle:   在Mac下为终端设置快捷键,创建工作流，调试AppleScript
date:       2017-02-06
author:     kunnan
header-img: img/post-bg-re-vs-ng2.jpg
catalog: true
tags:
    - Mac
    - 效率
    - 开发技巧
---

# 前言

>在Mac下快速调出终端的方法是：采用 Automator 创建服务，为服务创建快捷键。

```
An Apple app that comes with macOS and
lets you create workflows for automating repetitive tasks. 
Automator works with many other apps, 
including the Finder, Safari, Calendar, Contacts, Microsoft Office, and Adobe Photoshop.
```

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

```
 在Services 中开启 new Terminal at Folder  等系统功能以及对应的快捷键
```

## 黑技能

#### Automator扩展

>* 创建一个`Shell`脚本服务

```
勾选:`用输出内容替换所选文本`

输入：`sort|uniq` 
```

#### other

>* [文本转语音](http://25.io/toau/)

```
cat sample.txt | say -o sample.aiff
```


#### 使用终端 显示/隐藏 文件

>* 使用命令设置finder的AppleShowAllFiles属性

```
 devzkndeMBP:kunnan.github.io.git devzkn$ defaults write com.apple.finder AppleShowAllFiles -boolean true ; killall Finder

 devzkndeMBP:kunnan.github.io.git devzkn$ defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder
```

>* 创建alias 到.bash_profile

```
devzkndeMBP:~ devzkn$ echo -e  "\nalias fh='defaults write com.apple.finder AppleShowAllFiles -boolean false ; killall Finder'">> ~/.bash_profile && source ~/.bash_profile

```


####  List of acceptable shells for chpass(1).

```
devzkndeMBP:kunnan.github.io.git devzkn$ cat /etc/shells

/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh

配置bash的时候，采用~/.bash_profile；配置zsh的时候，采用open ~/.zshrc 
```

>* [ chpass, chfn, chsh -- add or change user database information]

```

     /etc/shells         the list of approved shells
     /etc/chpass.XXXXXX  temporary copy of the data to edit
     SEE ALSO: login(1), passwd(1), getusershell(3), passwd(5)  

<!-- 设置为默认shell:    usage: chpass [-l location] [-u authname] [-s shell] [user] -->
    chsh -s /bin/zsh

```

>* 安装 oh my zsh 配置zsh

```
	git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
	cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

	<!--  修改主题 -->
	open ~/.zshrc 
	修改 `ZSH_THEME=”robbyrussell”`，主题在 ~/.oh-my-zsh/themes 目录下。
```

>* 修改 zsh 配置文件

```
alias atom='/Applications/Atom.app/Contents/MacOS/Atom'
alias subl='/Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl'
alias code='/Applications/Visual\ Studio\ Code.app/Contents/Resources/app/bin/code'
# open -b com.sublimetext.3 ${postName}
# open -b com.uranusjr.macdown ${postName}
```


#### 快捷键开启touch bar

```
#macOS 10.12.1  + Xcode
打开Xcode 使用shift+ command+5
```

#### finder 的快捷键

>* 创建目录

```
shift +command+N 
untitled folder
```

>* 关闭finder

```
option+command+w
#或者使用，killall -9 Finder
```

# see also

>* [oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)

>* [用React、Redux、Immutable做俄罗斯方块](https://github.com/chvin/react-tetris)

```
8、开发
npm install
npm start
<!-- 打包编译 -->
npm run build
https://github.com/idetool/react-tetris
```

