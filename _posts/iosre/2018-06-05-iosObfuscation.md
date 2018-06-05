---
layout: post
title: iosObfuscation
date: 2018-06-05
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 马甲包混淆方案
---


# 马甲包混淆方案

>* 通过技术手段，多次上架同一款产品的方法.
>* 另一种马甲包是内容和功能完全不合规，壳子本身功能正常是为了能通过审核，而一旦审核通过，大部分都会直接成为一个web展示内容，包括：赌博、色情、以及其他（我司是网赚平台）

# 混淆方案组合一 混淆调用树




# [混淆方案组合二 混淆方法名，类名](https://zhangkn.github.io/2018/04/iOSobfuscation/)


#### [原版](https://blog.csdn.net/yiyaaixuexi/article/details/29201699)

提供两个文件：func.list、confuse.sh

```
将要混淆的方法名、类名 填入func.list 文件里面。项目里不要引入func.list文件

Build Phases 点击’+’,new run script , Run Script -> add $PROJECT_DIR/confuse.sh

command + B 将生成的 codeObfuscation.h加入项目
```

>* [confuse.sh：简易的混淆脚本，主要思路是把敏感方法名集中写在一个名叫func.list的文件中，逐一#define成随机字符，追加写入.h。------痛点就是一个一个手写](https://gist.github.com/zhangkn/dc2f5e5a883cc506d20fc6285775c313)


# 注意

>使用这套方案，（2种）目的是为了混淆我们的SDK
>```
>1)目前暂未出现因为机审不过的情况，通常是因为马甲本身的功能不够丰富。
>2)另外方案一有可能导致当前项目无法使用xib以及无法继承自定义类的情况，暂时无法解决，发生几率不高，但是还是建议在工程做完之后再修改编译方式。
>```


# See Also 

#### iOSobfuscation

>* [obfuscator](https://github.com/iOSobfuscation/obfuscator)
>* [Hikari: 基于 obfuscator 进行了Xcode9的适配](https://github.com/iOSHacking/Hikari)
>
>* [马甲包混淆方案:组合 Hikari+ 混淆方法名，类名](http://biqinglin.com/2018/05/06/%E9%A9%AC%E7%94%B2%E5%8C%85%E6%B7%B7%E6%B7%86%E6%96%B9%E6%A1%88/#more)

>* [back zip hikari](https://github.com/iOSobfuscation/KNHikari)

```
1) http://7xunik.com1.z0.glb.clouddn.com/Hikari.zip
```
>*  Uploading LFS objects

```
2) Uploading LFS objects
remote: error: File Hikari.zip is 185.04 MB; this exceeds GitHub's file size limit of 100.00 MB
remote: error: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.github.com.
devzkndeMacBook-Pro:Hikari devzkn$ git lfs track "*.zip" 
Tracking "*.zip"
devzkndeMacBook-Pro:Hikari devzkn$ kngitinit git@github.com:iOSobfuscation/KNHikari.git
Uploading LFS objects:   0% (0/1), 114 MB | 591 KB/s                                                                                                                                                                         
```

#### other

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost iosObfuscation 马甲包混淆方案 -t iosre
> #原来""的参数，需要自己加上""
```

