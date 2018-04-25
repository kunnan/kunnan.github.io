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




# 建立子目录与git仓库的联系

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

至此，已经将外部项目关联到主项目的子目录。


# 同步更新主项目中的基础库


#### 1、同步更新主项目中的基础库

当基础库更新之后，引用基础库的主项目也需要同步更新.

>* 与添加联系的命令类似：
>```
>remote add -f 换成fetch
>git subtree add 换成 git subtree pull
>```
>
>* 同步更新共需要以下两条git命令
>```
>git fetch <基础库仓库名> <基础库分支>
git subtree pull --prefix=<基础库在主项目中的子目录> <基础库仓库名> <基础库分支> --squash
让用户填写本次基础库更新的commit，可以直接Ctrl+X退出，或者自己写对应的更新内容
>```

#### 2、更新实例演示

>* 将远程仓库更新同步到本地仓库KNCocoaTouchStaticLibrary
>
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git fetch git@github.com:zhangkn/KNCocoaTouchStaticLibrary.git master
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:zhangkn/KNCocoaTouchStaticLibrary
 * branch            master     -> FETCH_HEAD
>```

>* 将本地仓库 KNCocoaTouchStaticLibrary 拉到对应子目录更新
>
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree pull --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master --squash
Working tree has modifications.  Cannot add.
//这个是项目的版本冲突了，解决的方式有很多种（git checkout -- <file>..、或者项目重置），我这边就使用kngitreset
>```

>* kngitreset
>```
>  git reset --hard
  git clean -fdx
>```


>*  确保
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree pull --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master --squash
From github.com:zhangkn/KNCocoaTouchStaticLibrary
 * branch            master     -> FETCH_HEAD
   47de4c7..bbf24c6  master     -> KNCocoaTouchStaticLibrary/master
>```
>
# 同步主项目中基础库的修改

引用基础库，用户不应该修改它，而是在基础库的独立项目中修改，然后再推送更新。当然主项目中也可以修改基础库。


#### 1、git命令

>* 子目录的内容同步到基础库仓库名对应的远程仓库的指定分支
>
>```
>git subtree push --prefix=<基础库在主项目中的子目录> <基础库仓库名> <基础库分支>
>```


#### 2、推送基础库更新到远程仓库

>* 推送基础库更新到远程仓库
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree push --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master 
>```
>


# see also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost git_subtree 以子目录形式引用外部项目 -t Git
> #原来""的参数，需要自己加上""
```

