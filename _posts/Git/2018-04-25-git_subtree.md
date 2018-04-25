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

最近搭建的[静态库工程模板](https://github.com/zhangkn/KNCocoaTouchStaticLibrary)，被引用到[主工程](https://github.com/zhangkn/KNAPP)中；为了维护的方便，便采用了git_subtree 进行管理


>* 通用背景(主项目、公共基础库)
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

>* 本文采用直接操作git url;但是推荐`先在本地建立util.git版本库的追踪分支`
>```
>//方式一： 直接操作分支util-branch名称
>$ git remote add util /path/to/repos/util.git
$ git fetch util
$ git checkout -b util-branch util/master
$ git subtree add -P lib util-branch
//方式二：直接操作git URL
$ git subtree add -P lib /path/to/repos/util.git master
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
ps: error: There was a problem with the editor 'vi'.
Not committing merge; use 'git commit' to complete the merge. 这个时候使用git commit 即可解决
>```
>

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


>*  确保working tree clean  之后，再进行git subtree pull
>
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree pull --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master --squash
From github.com:zhangkn/KNCocoaTouchStaticLibrary
 * branch            master     -> FETCH_HEAD
   47de4c7..bbf24c6  master     -> KNCocoaTouchStaticLibrary/master
>```
>
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

>* 无内容修改的时候，推送基础库更新到远程仓库
>```
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree push --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master 
git push using:  KNCocoaTouchStaticLibrary master
Everything up-to-date
>```

>* 有内容修改的时候，进行push
>```
>>devzkndeMacBook-Pro:KNAPP devzkn$ git add .
devzkndeMacBook-Pro:KNAPP devzkn$ git commit -a
>devzkndeMacBook-Pro:KNAPP devzkn$ git subtree push --prefix=KNCocoaTouchStaticLibrary KNCocoaTouchStaticLibrary master 
git push using:  KNCocoaTouchStaticLibrary master
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 682 bytes | 682.00 KiB/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 3 local objects.
To github.com:zhangkn/KNCocoaTouchStaticLibrary.git
   bbf24c6..b7e3e0a  b7e3e0a2ac30f3b92c8661d42aa51eb36f6149b1 -> master
>```


>*  分支图: git log --graph --pretty=oneline
>
>```
>git log --graph --pretty=oneline
>* 4af6e9bc9c463310fbb511397f3fd8ee079f2312 (HEAD -> master, origin/master, origin/HEAD) git_subtree
* 4e8a4494a1ad9687a5923099a0157e6d7af1a866 push:
*   bcc7c2aeb71a346a9e83ea4e9954d2b5f2d11e33 README.md
|\  
| * 3928c19dddb8c7e8ad15a222523c5a370a455b33 Squashed 'KNCocoaTouchStaticLibrary/' changes from 47de4c7..bbf24c6
* | f48b5cdb8eec56c2226e4fde486a32e0728e89da README.md
* |   89528acc3cee61542ca5fb5aff95bd33a7ffe210 Merge commit '96551081b6604407e2f444a8e6a7ca3d75947cea' as 'KNCocoaTouchStaticLibrary'
|\ \  
| |/  
| * 96551081b6604407e2f444a8e6a7ca3d75947cea Squashed 'KNCocoaTouchStaticLibrary/' content from commit 47de4c7
*   d41cdc4a2ab2b918de7d0f761aa2c1c5dc8f6566 Merge branch 'master' of github.com:zhangkn/KNAPP
|\  
| * 32801e86c1c694797d7b2f267b18fba0968c61ad Update README.md
| * 9bbbf25cc99c98bbe7ac58951e8fb2050bdb1405 Update README.md
| * 88930444966f233765f950cf96eaa6743c89f9a1 Update README.md
| * 630e5456f4a0d87624ecf9de98f252c27ea09764 Update README.md
| * 6f4a4654d9e70942141f5d842fb3211aca8ca637 Update README.md
| * 3a4721785ed3d6022cd450b86b1e2b73cb193028 Update README.md
| * f70d60946a44d90a9f3c2c610cf7a5a27e62d42d Create README.md
* | 89faa67857abac9453db0329657b58d0a04f7516 KNCocoaTouchStaticLibrary
|/  
* 980e96b64a42c3aaf33734d13f8b3a704a99f1de KNCocoaTouchStaticLibrary
* a549563432d80bfbe252093d0b3e72edb17a6bb5 KNapp的整体代码在KNAPP.zip 中（包括app工程以及静态库工程） https://github.com/zhangkn/KNCocoaTouchStaticLibrary https://github.com/zhangkn/KNAPP
* c302d2953806deaa95ce86db64df75aee6c22dfd 10、配置bundle工程的infoPlist文件路径 ![这里写图片描述](http://img.blog.csdn.net/20170629190534799?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvejkyOTExODk2Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
* 0f9299d60f1787c599e66ca8ea5e410d85e7730c Initial Commit
>```


# 附：git subtree插件的介绍

#### [commandgit-subtree-add](https://github.com/zhangkn/gotgit/blob/master/04-git-model/050-subtree-model.rst#commandgit-subtree-add)

>* 命令:command:`git subtree add`相当于将其他版本库以子树方式加入到当前版本库。用法：
>```
>git subtree add [--squash] -P <prefix> <commit>
git subtree add [--squash] -P <prefix> <repository> <refspec>
--squash含义为压缩为一个版本后再添加
>```
 
>* 例子: 将util.git合并到main.git的:file:`lib`目录
>```
>$ git subtree add -P lib /path/to/repos/util.git master
>推荐:先在本地建立util.git版本库的追踪分支
>$ git remote add util /path/to/repos/util.git
>$ git fetch util
$ git checkout -b util-branch util/master
$ git subtree add -P lib util-branch
>```
>

#### :command:`git subtree merge`

>* 相当于将子树对应的远程分支的更新重新合并到子树中
>```
>git subtree merge [--squash] -P <prefix> <commit>
>--squash含义为压缩为一个版本后再合并
>```

>* 将util-branch分支包含的上游最新改动合并到master分支的:file:`lib`目录
>```
>$ git subtree merge -P lib util-branch
>```

#### :command:`git subtree pull`

>* 相当于先对子树对应的远程版本库执行一次:command:`git fetch`操作，然后再执行:command:`git subtree merge`。
>```
>git subtree pull [--squash] -P <prefix> <repository> <refspec...>
>```

>* 将util.git版本库的master分支包含的最新改动合并到master分支的:file:`lib`目录
>
>```
>$ git subtree pull -P lib /path/to/repos/util.git master
>```
>
#### :command:`git subtree split`

>* 相当将目录拆分为独立的分支，即子树拆分。拆分后形成的分支可以通过推送到新的版本库实现原版本库的目录独立为一个新的版本库
>```
>git subtree split -P <prefix> [--branch <branch>] [--onto ...] [--ignore-joins] [--rejoin] <commit...>
>```
>
>* 说明：
>```
>该命令的总是输出子树拆分后的最后一个commit-id。这样可以通过管道方式传递给其他命令，如:command:`git subtree push`命令。
参数--branch提供拆分后创建的分支名称。如果不提供，只能通过:command:`git subtree split`命令提供的提交ID得到拆分的结果。
参数--onto参数将目录拆分附加于已经存在的提交上。
参数--ignore-joins忽略对之前拆分历史的检查。
参数--rejoin会将拆分结果合并到当前分支，因为采用ours的合并策略，不会破坏当前分支。
>```

#### :command:`git subtree push`

>* 先执行子树拆分，再将拆分的分支推送到远程服务器
>
>```
>git subtree push -P <prefix> <repository> <refspec...>
>```
>

# see also 

>* [code git-subtree](http://github.com/apenwarr/git-subtree/)

>* [git subtree插件](https://github.com/gotgit/gotgit/blob/master/04-git-model/050-subtree-model.rst#git-subtree%E6%8F%92%E4%BB%B6)
>```
>Git subtree插件用shell脚本开发，安装之后为Git提供了新的:command:`git subtree`命令
>将其他版本库以子树形式导入，再也不必和底层的Git命令打交道了
>Gitsubtree 插件的作者将代码库公布在Github上：http://github.com/apenwarr/git-subtree/。
>安装Git subtree很简单：
>$ git clone git://github.com/apenwarr/git-subtree.git
$ cd git-subtree
$ make doc
$ make test
$ sudo make install
>```

>* [050-subtree-model.rst](https://github.com/gotgit/gotgit/blob/master/04-git-model/050-subtree-model.rst)

>* [git命令](http://www.zhengyh.com/git-command/)
>* [利用hexo、github搭建博客](http://www.zhengyh.com/set-up-a-blog-on-github-pages-with-hexo/)
>* [app.didiyun.com](https://app.didiyun.com/#/dc2/add?offeringUuid=939806502c73cb9d7550ca4145921bf6&bandwidthUuid=685bae3b6e2c46a0b6fc2fce06f343c7)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost git_subtree 以子目录形式引用外部项目 -t Git
> #原来""的参数，需要自己加上""
```

