---
layout:     post
title:      CocoaPods
subtitle:   CocoaPods的安装和使用
date:       2017-04-13
author:     
header-img: img/post-bg-hacker.jpg
catalog: true
tags:
    - iOS
    - Xcode
    - CocoaPods
    - ruby
---

# 前言



# 正文


#### 安装

**CocoaPods** 是用 ruby 实现的，要想使用它首先需要有 ruby 的环境。

#### 升级ruby

	查看ruby版本 
	$ ruby -v
	
	ruby 2.0.0p648 (2015-12-16 revision 53162) [universal.x86_64-darwin16]


使用 **rvm** 管理ruby：安装 ruby，升级ruby
	
	curl -L get.rvm.io | bash -s stable 
	source ~/.bashrc
	source ~/.bash_profile

切换 ruby 源


	$ gem sources --add https://gems.ruby-china.org/ --remove https://rubygems.org/
	$ gem sources -l
	*** CURRENT SOURCES ***
	
	https://gems.ruby-china.org
	
安装并切换 ruby

> 安装 2.3.3版本，适配其他工具对ruby的版本要求~

	rvm install 2.3.3 --disable-binary
	rvm use 2.3.3 --default
	

有关RVM的使用可以看这篇 [RVM 使用指南](http://qiubaiying.github.io/2017/04/28/RVM-使用指南/)

#### 安装CocoaPods

1. 安装
	
		sudo gem install -n /usr/local/bin cocoapods
2. 升级版本库

		pod setup
		
	这里需要下载版本库（非常庞大），需要等很久
	
		Receiving objects:  72% (865815/1197150), 150.07 MiB | 190.00 KiB/s
	
	或者直接从其他装有cocoapod的电脑中拷贝`~/.cocoapods`到你的用户目录，然后再 `pod setup`会节省不少时间
	
# 使用

#### 创建 `podfile` 文件


pod 已经提供了创建 `podfile` 文件的命令，在工程目录下

	pod init
	

#### 加载代码库：pod install 、pod update

下面的命令，直接在本地版本库中查找对应的代码库信息，不升级版本库，节省时间

	pod install --verbose --no-repo-update

若找不到库，再使用下面的命令

	pod install

#### 版本号

对版本号的操作除了指定与不指定(最新版本)，你还可以做其他操作：

-  `\>0.1`  高于0.1的任何版本
- `\>=0.1`  版本0.1和任何更高版本
- `<0.1`  低于0.1的任何版本
- `<=0.1`  版本0.1和任何较低的版本
- `〜>0.1.2`  版本 0.1.2的版本到0.2 ，不包括0.2。

# see also

- [CocoaPods公有仓库的创建](http://qiubaiying.top/2017/03/08/CocoaPods%E5%85%AC%E6%9C%89%E4%BB%93%E5%BA%93%E7%9A%84%E5%88%9B%E5%BB%BA/)
- [CocoaPods私有仓库的创建](http://qiubaiying.top/2017/03/10/CocoaPods%E7%A7%81%E6%9C%89%E4%BB%93%E5%BA%93%E7%9A%84%E5%88%9B%E5%BB%BA/)