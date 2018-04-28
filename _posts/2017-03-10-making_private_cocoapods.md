---
layout:     post
title:      making_private_cocoapods
subtitle:   use a private Spec Repo to do sharing components across projects：deploy Podspecs to your own private specs repo with` pod repo push REPO [NAME.podspec]`
date:       2017-03-10
author:   
catalog: true
tags:
    - iOS
    - CocoaPods
    - Git
---

# 前言

deploy Podspecs to your own private specs repo with` pod repo push REPO [NAME.podspec]`


>*  a few steps to getting a private pods setup for your project
>```
>1) creating a private repository for them, 
>2) letting CocoaPods know where to find it 
>3) and adding the podspecs to the repository.
```

>* [ source directive](https://guides.cocoapods.org/syntax/podfile.html#source)
>```
>source 'URL_TO_REPOSITORY'
>```
>

# [An Example](https://guides.cocoapods.org/making/private-cocoapods.html)

#### 1.  Create a Private Spec Repo

Create a repo on your server. This can be achieved on Github or on your own server as follows


>* 之前使用`pod trunk push KNIosCommonTool.podspec`[发布到 publicly available](https://kunnan.github.io/2018/04/26/pod_lib_create/)
>```
>Updating spec repo `master`
>对 `master` 仓库 进行合并、提交.[master仓库地址](https://github.com/CocoaPods/Specs) 
>```
>
>* The rest of this example uses the repo at [zhangkn Specs](https://github.com/zhangkn/Specs)
>```
>创建一个像 `master` 一样的存放版本描述文件的git仓库. 不失一般性，我选择了github.com 的 public repo。
>devzkndeMacBook-Pro:git devzkn$ pwd
/Users/devzkn/code/git
devzkndeMacBook-Pro:git devzkn$ mkdir Specs.git
devzkndeMacBook-Pro:git devzkn$ cd Specs.git
devzkndeMacBook-Pro:Specs.git devzkn$ git init --bare
Initialized empty Git repository in /Users/devzkn/code/git/Specs.git/
 kngitinit git@github.com:zhangkn/Specs.git
>```
>* 你可以使用 [oschina](http://git.oschina.net/)
>```
>>私人git仓库，选择 [oschina](http://git.oschina.net/) 创建远程私有仓库（free）也可以在GitHub上创建（**money**）。
>```
>

#### 2.  Add your repo to your CocoaPods installation 


>*  pod repo add  zhangkn_specs git@github.com:zhangkn/Specs.git
>```
>Cloning spec repo `zhangkn_specs` from `git@github.com:zhangkn/Specs.git`
>```
	
>*  cd `~/.cocoapods/repos`
>```
>devzkndeMBP:repos devzkn$ tree -L 1
.
├── AliBCSpecs
├── artsy
├── master
├── specta
└── zhangkn_specs
>```
>
>* Check your installation is successful
>```
>cd ~/.cocoapods/repos/zhangkn_specs
>pod repo lint .
>Linting spec repo `zhangkn_specs`
>All the specs passed validation.
>```
>


#### 3、Add your Podspec to your repo


>* [Create your Podspec](https://kunnan.github.io/2018/04/26/pod_lib_create/)
>```
>pod lib lint  KNBaseWebViewController.podspec --verbose  --allow-warnings
>git tag -a 0.1.1 -m "0.1.1"
>git push origin --tags
> # pod trunk push KNIosCommonTool.podspec  这个步骤替换成pod repo push zhangkn_specs /Users/devzkn/code/cocoapodDemo/podDemo/KNBaseWebViewController/KNBaseWebViewController.podspec --verbose  --allow-warnings
>```
	

>* Save your Podspec and add to the repo
>```
>pod repo push zhangkn_specs /Users/devzkn/code/cocoapodDemo/podDemo/KNBaseWebViewController/KNBaseWebViewController.podspec --verbose  --allow-warnings
>Updating the `zhangkn_specs' repo
>Adding the spec to the `zhangkn_specs' repo
>Pushing the `zhangkn_specs' repo
>/usr/bin/git -C /Users/devzkn/.cocoapods/repos/zhangkn_specs -C /Users/devzkn/.cocoapods/repos/zhangkn_specs push origin master
>```

>* Assuming your Podspec validates, it will be added to the repo. The repo will now look like this
>```
>cd  /Users/devzkn/.cocoapods/repos/zhangkn_specs 
>devzkndeMBP:zhangkn_specs devzkn$ tree -L 3
.
├── HEAD
├── KNBaseWebViewController
│   └── 0.1.1
│       └── KNBaseWebViewController.podspec
├── README.md
>```
>



>*  pod repo update zhangkn_specs
	

>* pod search KNBaseWebViewController
>
>```
>-> KNBaseWebViewController (0.1.1)
   KNPodlib Improve  customize webview functionality
   pod 'KNBaseWebViewController', '~> 0.1.1'
   - Homepage: https://github.com/zhangkn/KNBaseWebViewController
   - Source:   https://github.com/zhangkn/KNBaseWebViewController.git
   - Versions: 0.1.1 [zhangkn_specs repo]
>```
>

	
###  See this Podfile for an example of how the repo URL is included

>* [demo repo](https://github.com/kunnan/KNBaseWebViewControllerDemo)
>
>* [demo for `how the private repo URL is included` ](https://github.com/kunnan/KNBaseWebViewControllerDemo/blob/master/Podfile)
>```
> pod init
> source 'https://github.com/zhangkn/Specs.git'
> Cloning spec repo `zhangkn` from `https://github.com/zhangkn/Specs.git`
> devzkndeMBP:repos devzkn$ tree -L 1
.
├── AliBCSpecs
├── artsy
├── master
├── specta
├── zhangkn # 从https://github.com/zhangkn/Specs.git 下载的仓库
└── zhangkn_specs # 我刚刚创建的本地仓库
 open KNBaseWebViewControllerDemo.xcworkspace
 pod update
 Installing KNBaseWebViewController 0.1.2 (was 0.1.1)
>```


>* 使用私人pod库的需要在`Podflie`中添加这句话，指明你的版本库地址。
>```
> source 'https://github.com/zhangkn/Specs.git'
>```

	
>* 如果同时使用了其他的Specs库，也要声明
>```
	source ‘https://github.com/CocoaPods/Specs.git’
```


# How to remove a Private Repo

>* How to remove a Private Repo
```
pod repo remove [name]
```


# Making CocoaPods

>* [making-a-cocoapod](https://guides.cocoapods.org/making/making-a-cocoapod.html)
>* [using-pod-lib-create](https://guides.cocoapods.org/making/using-pod-lib-create.html)
>* [getting-setup-with-trunk](https://guides.cocoapods.org/making/getting-setup-with-trunk.html)
>* [quality-indexes](https://guides.cocoapods.org/making/quality-indexes.html)
>* [private-cocoapods](https://guides.cocoapods.org/making/private-cocoapods.html)
>* [pecs-and-specs-repo](https://guides.cocoapods.org/making/specs-and-specs-repo.html)
>

# Reference

>* [syntax-podfile](https://guides.cocoapods.org/syntax/podfile.html)
>* [syntax-podspec](https://guides.cocoapods.org/syntax/podspec.html)
>* [terminal-commands.](https://guides.cocoapods.org/terminal/commands.html)
>

# see also

>* [artsy/Specs](https://github.com/artsy/Specs)
>
>


# External resources

>* [architecting-a-large-ios-app-with-cocoapods](http://dev.hubspot.com/blog/architecting-a-large-ios-app-with-cocoapods)
>
>```
>Using CocoaPods to Modularize a Big iOS App
>```
>
>
>




