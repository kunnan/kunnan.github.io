---
layout:     post
title:      useful_Git_Command
subtitle:   常用的Git指令:https://github.com/zhangkn/KNBin
date:       2017-02-15
author:     kunnan
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Mac
    - Terminal
    - Git
---

>整理的一些自用的Git指令

# 常用git脚本

#### 切换到历史提交版本

>* `git checkout 1b3acc187c1f78d666e2b428d32a1bceef6319b6`

```
 git checkout 1b3acc187c1f78d666e2b428d32a1bceef6319b6
 You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.
   git checkout -b <new-branch-name>
```

#### [GitHub创建仓库并提交到远程:kngitinit](https://github.com/zhangkn/KNBin/blob/master/kngitinit)

```
 usage: kngitinit  SSHURL

	echo "# 项目名" >> README.md
	git init
	git add README.md
	git commit -m "first commit"
	git remote add origin git@github.com:/项目名.git
	git push -u origin master
```

#### 创建分支develop ，并提交到远程仓库;

>* git push origin develop:develop
>```sh 
>git checkout -b develop
#  提交本地develop分支作为远程的develop分支
git push origin develop:develop
# 本地分支和远程分支建立联系(使用git branch -vv 可以查看本地分支和远程分支的关联关系)
#git branch --set-upstream-to=origin/远程分支的名字 本地分支的名字
git branch --set-upstream-to=origin/develop develop
>```


#### [若仓库存在,直接add、commit、push](https://github.com/zhangkn/KNBin/blob/master/kngit)

```
	git add .
	git commit -m $1
	#git remote add origin git@github.com:/test.git--- 只需要设置一次
	git push -u origin master
```


#### [git branch -d test]

```
<!-- 1、删除命令可以切换到其他分支，如 develop -->
git branch -d test

<!--2、【可选】 ,此时我在test 分支的源分支develop，删除了test分支之后 ；此时执行pull test 相当于合并了远程的test到develop -->
git pull origin test

直接合并Merge branch 'test' of 远程仓库 into 本地 develop

<!-- 3、使用推送分支命令时要使用一个特殊的引用表达式（冒号前为空）,进行远程分支test的删除 -->
 git push origin :test
 To gitlab..cn:/.git
 - [deleted]         test
```

#### [kngitreset](https://github.com/zhangkn/KNBin/blob/master/kngitreset)

```
#还原本地修改到基版本： 加上-–hard参数，则缓冲区中不会存储这些修改，git会直接丢弃这部分内容。
#可以使用 git push origin HEAD --force 强制将分区内容推送到远程服务器。
  git reset --hard
  git clean -fdx
```


#### [git 常用合并命令]

```
#切换回master分支

git checkout master

# merge  --no-ff参数，表示禁用Fast forward;可以保存你之前的分支历史。能够更好的查看merge历史，以及branch 状态.
#保证版本提交、分支结构清晰
git merge --no-ff  develop

#push
git push
```


#### [以当前分支为源创建分支test](https://github.com/zhangkn/KNBin/blob/master/knco)

```

# 用法： knco 分支名称

git checkout -b $1

#  提交本地develop分支作为远程的develop分支

git push origin $1:$1

#git branch --set-upstream-to=origin/远程分支的名字 本地分支的名字

git branch --set-upstream-to=origin/$1 $1

# 本地分支和远程分支建立联系(使用git branch -vv 可以查看本地分支和远程分支的关联关系)
```


#### [更多辅助脚本清点击这里](https://github.com/zhangkn/KNBin)



# 常用操作

#### 创建仓库（初始化）
	在当前指定目录下创建
	git init
	
	新建一个仓库目录
	git init [project-name]
	
	克隆一个远程项目
	git clone [url]
	
#### 添加文件到缓存区

	添加所有变化的文件
 	git add .

	添加名称指定文件
	git add text.txt

#### 配置

	设置提交代码时的用户信息
	git config [--global] user.name "[name]"
	git config [--global] user.email "[email address]"
	
	
#### 提交
	提交暂存区到仓库区
	git commit -m "msg"
	
	# 提交暂存区的指定文件到仓库区
	$ git commit [file1] [file2] ... -m [message]
	
	# 提交工作区自上次commit之后的变化，直接到仓库区
	$ git commit -a
	
	# 提交时显示所有diff信息
	$ git commit -v
	
	# 使用一次新的commit，替代上一次提交
	# 如果代码没有任何新变化，则用来改写上一次commit的提交信息
	$ git commit --amend -m [message]
	
	# 重做上一次commit，并包括指定文件的新变化
	$ git commit --amend [file1] [file2] ...
	
#### 远程同步

	# 下载远程仓库的所有变动，
	$ git fetch [remote]
	
	# 取回远程特定的cydia分支

	devzkndeMacBook-Pro:electra devzkn$ git fetch origin  cydia


	# 总结： 创建并Switched本地分支，并取回合并远程的一个cydia分支

	devzkndeMacBook-Pro:electra devzkn$ git checkout -b cydia origin/cydia

	devzkndeMacBook-Pro:electra devzkn$ git fetch origin




	# 显示所有远程仓库
	$ git remote -v
	
	# 显示某个远程仓库的信息
	$ git remote show [remote]
	
	# 增加一个新的远程仓库，并命名
	$ git remote add [shortname] [url]
	
	# 取回远程仓库的变化，并与本地分支合并
	$ git pull [remote] [branch]
	
	# 上传本地指定分支到远程仓库
	$ git push [remote] [branch]
	
	# 强行推送当前分支到远程仓库，即使有冲突
	$ git push [remote] --force
	
	# 推送所有分支到远程仓库
	$ git push [remote] --all


	
#### 分支

	# 列出所有本地分支
	$ git branch
	
	# 列出所有远程分支: git ls-remote 查看远程仓库的
	$ git branch -r
	
	# 列出所有本地分支和远程分支: git branch --all
	$ git branch -a
	
	# 新建一个分支，但依然停留在当前分支
	$ git branch [branch-name]
	
	# 新建一个分支，并切换到该分支 ：git checkout -b develop
	$ git checkout -b [branch]
	
	# 新建一个分支，指向指定commit
	$ git branch [branch] [commit]
	
	# 新建一个分支，与指定的远程分支建立追踪关系
	$ git branch --track [branch] [remote-branch]
	
	# 切换到指定分支，并更新工作区
	$ git checkout [branch-name]
	
	# 切换到上一个分支
	$ git checkout -
	
	# 建立追踪关系，在现有分支与指定的远程分支之间
	$ git branch --set-upstream [branch] [remote-branch]
	
	# 合并指定分支到当前分支
	$ git merge [branch]
	
	# 选择一个commit，合并进当前分支
	$ git cherry-pick [commit]
	
	# 删除分支:  参数-D则可强制删除尚未合并的分支。
	$ git branch -d [branch-name]
	
	# 删除远程分支:  git push origin :vpn
	$ git push origin --delete [branch-name]
	$ git branch -dr [remote/branch]
	
#### 标签Tags

	添加标签 在当前commit
	git tag -a v1.0 -m 'xxx' 
	
	添加标签 在指定commit
	git tag v1.0 [commit]
	
	查看
	git tag
	
	删除
	git tag -d V1.0
	
	删除远程tag
	git push origin :refs/tags/[tagName]
	
	推送
	git push origin --tags
	
	拉取
	git fetch origin tag V1.0

	新建一个分支，指向某个tag
	git checkout -b [branch] [tag]

#### 查看信息

	# 显示有变更的文件，在遇到git 冲突的时候，常常可以利用git status 来查看解决方案
	$ git status
	
	# 显示当前分支的版本历史
	$ git log
	
	# 显示commit历史，以及每次commit发生变更的文件
	$ git log --stat
	
	# 搜索提交历史，根据关键词
	$ git log -S [keyword]
	
	# 显示某个commit之后的所有变动，每个commit占据一行：
	$ git log [tag] HEAD --pretty=format:%s
	
	# 显示某个commit之后的所有变动，其"提交说明"必须符合搜索条件
	$ git log [tag] HEAD --grep feature
	
	# 显示某个文件的版本历史，包括文件改名
	$ git log --follow [file]
	$ git whatchanged [file]
	
	# 显示指定文件相关的每一次diff
	$ git log -p [file]
	
	# 显示过去5次提交： git log --pretty=oneline
	$ git log -5 --pretty --oneline
	
	# 显示所有提交过的用户，按提交次数排序
	$ git shortlog -sn
	
	# 显示指定文件是什么人在什么时间修改过
	$ git blame [file]
	
	# 显示暂存区和工作区的差异
	$ git diff
	
	# 显示暂存区和上一个commit的差异
	$ git diff --cached [file]
	
	# 显示工作区与当前分支最新commit之间的差异
	$ git diff HEAD
	
	# 显示两次提交之间的差异
	$ git diff [first-branch]...[second-branch]
	
	# 显示今天你写了多少行代码
	$ git diff --shortstat "@{0 day ago}"
	
	# 显示某次提交的元数据和内容变化:  git show HEAD 查看最近一次更改。
	$ git show [commit]
	
	# 显示某次提交发生变化的文件
	$ git show --name-only [commit]
	
	# 显示某次提交时，某个文件的内容
	$ git show [commit]:[filename]
	
	# 显示当前分支的最近几次提交： git reset --hard b45959e ，利用这个命令查看回滚的版本ID
	$ git reflog
	
#### 撤销
	
	# 恢复暂存区的指定文件到工作区
	$ git checkout [file]
	
	# 恢复某个commit的指定文件到暂存区和工作区
	$ git checkout [commit] [file]
	
	# 恢复暂存区的所有文件到工作区
	$ git checkout .
	
	# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
	$ git reset [file]
	
	# 重置暂存区与工作区，与上一次commit保持一致 ： git reset --hard  32f87f5e93501b24d42c7aba49106dd9bed0b943
	$ git reset --hard
	
	# 重置当前分支的指针为指定commit，同时重置暂存区，但工作区不变
	$ git reset [commit]
	
	# 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
	$ git reset --hard [commit]
	
	# 重置当前HEAD为指定commit，但保持暂存区和工作区不变
	$ git reset --keep [commit]
	
	# 新建一个commit，用来撤销指定commit
	# 后者的所有变化都将被前者抵消，并且应用到当前分支
	$ git revert [commit]
	
	# 暂时将未提交的变化移除，稍后再移入
	$ git stash
	$ git stash pop
	
#### 其他

	# 生成一个可供发布的压缩包
	$ git archives

	#取消本地目录下关联的远程库;常常用于copyxx项目的基础上，创建新项目的场景

	git remote remove origin

# 例子

>*  分支图: git log –graph –pretty=oneline
>
>```
>分支图: git log –graph –pretty=oneline
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

# 使用 .gitignore 忽略 Git 仓库中的文件

.DS_Store、Xocde、pod

#### [格式](https://github.com/github/gitignore)

- `#` :此为注释 – 将被 Git 忽略
- `*.a` :忽略所有 `.a` 结尾的文件
- `!lib.a` : 不忽略 `lib.a` 文件
- `/TODO` :仅仅忽略项目根目录下的 `TODO` 文件,不包括 `subdir/TODO`
- `build/` : 忽略 `build/` 目录下的所有文件
- `doc/*.txt` : 会忽略 `doc/notes.txt` 但不包括 `doc/server/arch.txt`

#### [创建方法: 通过 gitignore.io（推荐）](https://www.gitignore.io/)

>* 自定义shell function 命令：

```
#macOS下默认是`\#!/bin/bash`：

	echo -e  "\nfunction gi() { curl -L -s https://www.gitignore.io/api/\$@ ;}" >> ~/.bash_profile && source ~/.bash_profile
```
>* [自定义shell :kngi](https://github.com/zhangkn/KNBin/blob/master/kngi)

```
<!-- code -->
curl -L -s https://www.gitignore.io/api/$1

<!-- 用法 -->
devzkndeMacBook-Pro:DeleteMe devzkn$ kngi swift
# Created by https://www.gitignore.io/api/swift
# End of https://www.gitignore.io/api/swift
```



# see also

>* [050-subtree-model.rst](https://github.com/gotgit/gotgit/blob/master/04-git-model/050-subtree-model.rst)
>
>* [git subtree 以子目录形式引用外部项目](http://www.zhengyh.com/git-subtree/)

>* [ Git Subtree命令剖析](http://jifengyin.tumblr.com/post/93389974636/git-subtree%E5%91%BD%E4%BB%A4%E5%89%96%E6%9E%90)
>
>* [https://zhangkn.github.io/2018/03/GitHub/](https://zhangkn.github.io/2018/03/GitHub/)

```
参与GitHub中的项目开发，最常用和推荐的首选方式是“Fork + Pull”模式。
1) 建立主页:GitHub用户通过创建特殊名称的Git版本库或在Git库中建立特别的分支实现对主页的维护

创建个人(组织)主页:GitHub 为每一个用户分配了一个二级域名.github.io， 例如我自己的site: https://zhangkn.github.io

项目主页:项目主页也通过二级域名进行访问 http://gotgithub.github.io/项目名称—https://zhangkn.github.io/AlipayWalletTweakF
```

>* [usefulCommand](https://zhangkn.github.io/2017/12/usefulCommand/)

>* [https://zhangkn.github.io/2018/02/Electra/](https://zhangkn.github.io/2018/02/Electra/)

```
<!-- 获取cydia 分支 -->

devzkndeMacBook-Pro:electra devzkn$  git branch -r

devzkndeMacBook-Pro:electra devzkn$ git fetch origin  cydia

<!-- 总结： 取回远程的一个分支 cydia-->

devzkndeMacBook-Pro:electra devzkn$ git checkout -b cydia origin/cydia
Branch cydia set up to track remote branch cydia from origin.
Switched to a new branch 'cydia'

devzkndeMacBook-Pro:electra devzkn$ git fetch origin
```

