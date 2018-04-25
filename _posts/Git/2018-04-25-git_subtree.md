---
layout: post
title: git_subtree
date: 2018-04-25
tag: Git
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 以子目录形式引用外部项目
---


# 前言

最近搭建的[静态库工程模板] (https://github.com/zhangkn/KNCocoaTouchStaticLibrary)，被引用到[主工程](https://github.com/zhangkn/KNAPP)中；为了维护的方便，便采用了git_subtree 进行管理


>* 需求通用背景(主项目、公共基础库)
>```
>如何在主项目中引用其他git项目（基础库）
>公共基础库更新了，引用基础库的主项目如何同步更新
>```

>* 本文的用法演示：[KNAPP主项目](https://github.com/zhangkn/KNAPP)、[公共的静态库](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)
>
>```
>想把KNCocoaTouchStaticLibrary作为子目录形式导入到KNAPP主项目中
>git@github.com:zhangkn/KNAPP.git
>git@github.com:zhangkn/KNCocoaTouchStaticLibrary.git
>devzkndeMacBook-Pro:KNAPP devzkn$ tree -L 2
.
├── KNAPP
├── KNCocoaTouchStaticLibrary
>```




# 流程


#### 1、初始化建立子目录与git仓库的联系

>*  建立联系共需要以下两条git命中
>```
>git remote add -f <基础库仓库名> <基础库git地址>
git subtree add --prefix=<基础库在主项目中的子目录> <基础库仓库名> <基础库分支> --squash
–-squash：把subtree子项目的更新记录进行合并成一个commit，再合并到主项目中,可选
>```

##### 2、初始化实例演示

>*  clone 主项目
>```
>devzkndeMacBook-Pro:cocoapodStatic devzkn$ git clone git@github.com:zhangkn/KNAPP.git
>```

>* git remote add -f  KNCocoaTouchStaticLibrary git@github.com:zhangkn/KNCocoaTouchStaticLibrary.git
>
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git remote add -f  KNCocoaTouchStaticLibrary git@github.com:zhangkn/KNCocoaTouchStaticLibrary.git
>Updating KNCocoaTouchStaticLibrary
warning: no common commits
remote: Counting objects: 78, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 78 (delta 0), reused 0 (delta 0), pack-reused 75
Unpacking objects: 100% (78/78), done.
From github.com:zhangkn/KNCocoaTouchStaticLibrary
 * [new branch]      master     -> KNCocoaTouchStaticLibrary/master
devzkndeMacBook-Pro:KNAPP devzkn$ git branch --all
* master
  remotes/KNCocoaTouchStaticLibrary/master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
>```
>
>* git subtree add --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master --squash
>
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree add --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master --squash
git fetch KNCocoaTouchStaticLibrary master
From github.com:zhangkn/KNCocoaTouchStaticLibrary
 * branch            master     -> FETCH_HEAD
Added dir 'KNCocoaTouchStaticLibrary'
>```
>
>* tree
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ tree -L 2
.
├── KNAPP
│   ├── AppDelegate.h
│   ├── AppDelegate.m
│   ├── Assets.xcassets
│   ├── Base.lproj
│   ├── Info.plist
│   ├── KNAPP.zip
│   ├── ViewController.h
│   ├── ViewController.m
│   └── main.m
├── KNAPP.xcodeproj
│   ├── project.pbxproj
│   ├── project.xcworkspace
│   └── xcuserdata
├── KNCocoaTouchStaticLibrary
│   ├── Business
│   ├── KNCocoaTouchStaticLibrary
│   ├── KNCocoaTouchStaticLibrary.xcodeproj
│   ├── KNStaticBundle
│   └── README.md
└── README.md
>```

>* git remote -v
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git remote -v
KNCocoaTouchStaticLibrary	git@github.com:zhangkn/KNCocoaTouchStaticLibrary.git (fetch)
KNCocoaTouchStaticLibrary	git@github.com:zhangkn/KNCocoaTouchStaticLibrary.git (push)
origin	git@github.com:zhangkn/KNAPP.git (fetch)
origin	git@github.com:zhangkn/KNAPP.git (push)
>```


# see also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost git_subtree 以子目录形式引用外部项目 -t Git
> #原来""的参数，需要自己加上""
```

