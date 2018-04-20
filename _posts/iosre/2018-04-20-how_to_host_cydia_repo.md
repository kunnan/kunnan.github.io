---
layout: post
title: how_to_host_cydia_repo
date: 2018-04-20
tag: iosre
site: https://zhangkn.github.io
catalog: true
---

# 背景



将Tweak部署和更新到大量设备上通常的解决方案，是搭建私有Cydia源 ；而非通常的make package install 、dpkg -i;

>* cydia_repo 的目录结构
>
>
```
deb 源本质上就是需要特定结构的目录
cydia--
         |--debs--*.deb
         |--Packages  :dpkg-scanpackages debs /dev/null > Packages ;Packages 文件中包含源中每个包文件的信息，包括文件路径、大小、依赖、架构及校验信息
         |--Packages.bz2 ：由Packages文件压缩而来, 命令行: bzip2 Packages；
         |--Packages.gz
         |--Release  ：是一个普通的文本文件，用于描述当前源的信息；这些信息会在 Cydia 的源列表及 Tweak 搜索列表中显示
         |--Release.gpg :Package Signatures,from our Release file. This file will be downloaded by the clients first and then is used to verify the validity of the Release file. gpg -abs -o Release.gpg Release
其中Packages.bz2 和debs 是必须，其他文件都是可选的。
```

# 正文


### Cydia源服务器搭建

>* 假设URL地址是 192.168.2.189/cydia, 本地的cydia目录来说, 结构如下
>
```
Cydia/
 --Release
 --Packages
 --Packages.gz
 --Packages.bz2
 --CydiaIcon.png
 --debs/
   --xxxx1.deb
   --xxxx2.deb
```

>* [具体请看辅助脚本](https://github.com/zhangkn/KNBin/blob/master/kncydia)
>
```
dpkg-scanpackages: info: Wrote 1 entries to output Packages file.
Serving HTTP on 0.0.0.0 port 8088 ...
192.168.2.156 - - [06/Feb/2018 18:50:04] "HEAD /
```

>* python -m SimpleHTTPServer 8088
>
```
测试的时候可以使用,这个命令简单的开启一个HTTPServer
```

###  部署方式
 ~/cydia 下执行python -m SimpleHTTPServer 8088  ，
 添加http://192.168.2.189:8088/cydia 源即可


>* add cydia_repo
>
```
1) 在prints 脚本添加：
function kncydia {
    echo "[将自己的源地址添加到cyida 中]"
        # install.exec "echo -e '127.0.0.1 localhost \n192.168.2.107 xx.com' > /etc/hosts"
      echo -e 'deb http://192.168.2.69:8088/ ./ ' > /private/etc/apt/sources.list.d/wl.list#-e 参数是为了使用换行符\n
     # 清理VPN的垃圾log
     echo "" > /private/var/log/ppp.log
}
2) 手动添加其实等价于修改以下文件
/private/etc/apt/sources.list.d/cydia.list -> /var/mobile/Library/Caches/com.saurik.Cydia/sources.list
```


### 文件目录功能解释

>* Packages
>
```
1）deb包索引文件, 保存了各个deb包的control文件的信息,以及各个deb包的文件信息.
2）该目录是 由 dpkg-scanpackages debs /dev/null > Packages 产生。
```

>* Packages.bz2
```
由Packages文件压缩而来, 命令行: bzip2 Packages
```

>* Release 
```
Cydia源配置文件,客户端通过下载此文件来读取cydia源信息;
Release文件几乎不用改, 只要准备好deb文件, 然后用dpkg-scanpackage命令生成Packages就可以了
```

>* CydiaIcon.png
```
an icon named "CydiaIcon.png" in your root dir so the user finds your repo at a glance.
```

### see also

- [how_to_host_cydia_repo](https://zhangkn.github.io/2018/02/how_to_host_cydia_repo/)
- [how_to_host_cydia_repo](https://xiaozhuanlan.com/topic/1789520463)

### 知识补充

>* cydia
>
```
由 Jay Freeman（saurik）和他的公司开发，用于安装、管理越狱设备上的第三方软件、插件。它移植了Debian上的包管理器dpkg并提供了图形化前端，方便普通用户使用。Cydia 中还有个 Cydia Store，提供付费的第三方应用。
```

>* CydiaSubstrate
```
 iOS7 之前也叫MobileSubstrate，也是由saurik开发的。Cydia Substrate consists of 3 major components: MobileHooker, MobileLoader and safe mode.
```

>* [Electra](https://github.com/coolstar/electra/blob/master/docs/getting-started.md)
>
```
 Tweak 开发者 CoolStar 基于 async_awake、以及Comex 开发的 CydiaSubstrate 的开源替代: Substitute，开发了 Electra 越狱工具。支持 iOS11.0 - iOS 11.1.2 的全部 iOS 设备
```
