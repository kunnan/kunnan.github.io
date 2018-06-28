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
    - Efficiency
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

>* [几种常见的Shell：sh、bash、csh、tcsh、ash](http://c.biancheng.net/cpp/view/6995.html)
>
>```
>Shell 既是一种脚本编程语言，也是一个连接内核和用户的软件。
>1) sh:  Bourne shell, 是 UNIX 上的标准 shell
>2) csh: 这个 shell 的语法有点类似C语言
>3) tcsh:  csh 的增强版，加入了命令补全功能
>4) ash: 简单的轻量级的 Shell
>5) bash: bash shell 是 Linux 的默认 shell;bash 兼容 sh
>devzkndeMBP:kunnan.github.io.git devzkn$ echo $0
-bash

一、查看默认 Shell，指明了当前使用的 Shell 程序的位置
devzkndeMBP:kunnan.github.io.git devzkn$  echo $SHELL
/bin/bash
>```

>* [iOS 多开检测，反多开检测，反反多开检测](http://iosre.com/t/ios/11611)

```
<!--1、 如何多开 -->

修改 Info.plist 中的 CFBundleIdentifier 。

1)为了防止 Info.plist 被恶意篡改，iOS 提供一种数字签名技术。通过该技术，计算出 Info.plist 文件的 Hash 值，加密后存入到签名文件中。在安装时与安装后，可通过该签名文件存的 Hash 值进行文件签名校验。

2) 可以把这种签名校验机制去掉，或者重新签名

<!-- 2、如何检测多开 -->

1）获取 App 的 Bundle Identifier 方法
NSBundle.mainBundle.bundleIdentifier;
[NSBundle.mainBundle objectForInfoDictionaryKey:@"CFBundleIdentifier"];
NSBundle.mainBundle.infoDictionary[@"CFBundleIdentifier"];
[NSDictioanry dictionaryWithContentsOfFile:@"Info.plist"][@"CFBundleIdentifier"]

<!-- 3、如何反检测多开：如果是系统、插件调用的方法，要返回真实的 Bundle ID。 -->

干掉上面的几个方法

1）通过 key 获取 value 的方式有多种，这几种方法也需要被干掉

info[@"CFBundleIdentifier"]; // -[NSDictionary objectForKeyedSubscript:]
[info objectForKey:@"CFBundleIdentifier"];
[info valueForKey:@"CFBundleIdentifier"];

https://github.com/Magic-Unique/MUHook

2）微信也可以通过 IDFA、DeviceName 来判断是否是同一台设备登录不同的微信

UIDevice、ASIdentifierManager

3） 通过 dyld 的 dladdr() 函数配合当前调用栈地址来判断调用者来自哪个二进制文件： 直接使用下层的那个函数而不是dladdr的话安全性又会高一些。
[NSThread callStackReturnAddresses]

<!-- 4、如何反反检测多开 -->

1） 使用 CoreFoundation 检测（CFBundleRef）
https://github.com/facebook/fishhook

2）使用 Appex、Watch 检测

3）使用 FILE + 加密 + 混淆 检测

4）依靠使用一些Obscure的检测方式并保持这些方式的保密性来确保App安全：http://bbs.iosre.com/t/llvm-hikari/10720/141?u=zhang
```

>* [macOS Sierra: 在设备间拷贝和粘贴](https://support.apple.com/kb/PH25168?locale=zh_CN&viewlocale=zh_CN)

```
1) 在“系统偏好设置”（在 Mac 上）和“设置”（在 iOS 设备上）中打开 Wi-Fi、蓝牙和 Handoff
2) 在所有设备上使用同一 Apple ID登录 iCloud
3)必须满足“连续互通”系统要求

```

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

