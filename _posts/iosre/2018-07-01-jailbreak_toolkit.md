---
layout: post
title: jailbreak_toolkit
date: 2018-07-01
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 越狱设备
---







# See Also 

#### ios8-9

> * OpenSSH 
> * adv-cmds: 执行 ps 命令报错，需要安装这个工具；
> * appsync: 让系统不再验证签名，以免安装应用失败；
> * [Cydia Substrate：允许第三方开发者在越狱系统的方法中打一些补丁或扩展方法。](https://kunnan.github.io/2018/07/01/MobileLoader/)



####  kim.cracksby.yalu102(ios10)

> * [优化一些配置信息：比如 dropbear.plist、bootstrap中包含scp、点击icon 自动激活](https://github.com/zhangkn/KNyalu102)
>
>   ```
>   ps:/Users/devzkn/code/re/yalu102-master
>   ```
>
>   > * [zhangkn.github.io](https://zhangkn.github.io/2018/01/kim.cracksby.yalu102/)
>   >
>   >   ```
>   >   从工程中看到解压tar 的命令的执行（可以自己封装一下）[jailbreak.m](https://github.com/zhangkn/KNyalu102/blob/master/yalu102/jailbreak.m)
>   >   ```
>   >
>   >   ```
>   >   手动安装scp:
>   >   https://coolstar.org/publicrepo/ 添加源
>   >   devzkndeMacBook-Pro:bin devzkn$ cat /Users/devzkn/code/re/yalu102-master/yalu102/scp | ssh usb2222 "cat > scp"
>   >   devzkndeMacBook-Pro:bin devzkn$ ssh usb2222 " chmod +x scp"
>   >   devzkndeMacBook-Pro:bin devzkn$ ssh usb2222 "mv scp /usr/bin/scp"
>   >   ```
>   >
>   >   ```
>   >   rsync的一大优势，是可以增量拷贝，即不覆盖原本已存在的文件。
>   >   在使用rsync前，需要在Cydia里搜索并下载rsync。安装完成后，使用方法如下（类比scp）：
>   >   scp
>   >   scp root@192.168.1.1:/var/log/syslog ~/
>   >   scp -P 2222 root@localhost:/var/log/syslog ~/
>   >   rsync
>   >   rsync -avzu --progress root@192.168.1.1:/var/log/syslog ~/
>   >   rsync -avzu --progress -e ssh -p 2222 root@localhost:/var/log/syslog ~/
>   >   即source和destination不变；
>   >   把scp改成rsync -avzu --progress
>   >   把scp -P 2222改成rsync -avzu --progress -e ssh -p 2222
>   >   ```
>   >
>   >   

#### Electra iOS 11.0 - 11.1.2

>* [Electra iOS 11.0 - 11.1.2 jailbreak toolkit based on async_awake](https://github.com/coolstar/electra)
>
>* >* [KNelectra](https://github.com/zhangkn/KNelectra)
>
>  ```
>   打开app 自动激活
>  
>   //todo 定制一些项目的需求： 比如创建初始化一些基本设置
>  ```
>
>  >* [async_awake](https://github.com/benjibobs/async_wake)
>  >* [Electra :zhangkn.github.io](https://zhangkn.github.io/2018/02/Electra/)
>
>* [逆向工具](https://mp.weixin.qq.com/s/uv-Bju1v1-y6TQntmHnCCg)
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost jailbreak_toolkit 越狱设备 -t iosre
> #原来""的参数，需要自己加上""
```

