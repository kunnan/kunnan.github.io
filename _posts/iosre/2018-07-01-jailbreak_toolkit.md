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



# Desktop tools



> * `Tweak` 工具：Theos（是一个越狱开发工具包，安装简单,Logos语法简单）、iOSOpenDev、MonkeyDev（支持CocoaPods,非越狱集成环境) 可以用来开发iPhone tool 、iPhone tweak 等
>
> * [optool](https://github.com/zhangkn/KNBin/blob/master/optool) 依赖注入工具,可将dylib注入到二进制文件中
>
> *  usbmuxd-1.0.8: 端口转发
>
> * [Hopper](https://www.hopperapp.com/) ;(同系列产品： [radare2](https://github.com/radare/radare2)、 [IDA_Pro_v7.0(MacOS)*and_Hex-Rays_Decompiler*(ARMx64,ARM,x64,x86).zip](https://down.52pojie.cn/Tools/Disassemblers/))  hopper 支持伪代码
>
> * lipo、[debugserver](http://iphonedevwiki.net/index.php/Debugserver)、lldb、[toggle-pie](https://github.com/zhangkn/KNtoggle-pie)
>
> * otool 、objdump(同系列工具：jtool ) -h -hv -vl
>
> * strings “实用工具专门用于提取文件中的字符串内容”
>
> * [去掉PIE:toggle-pie: disable ASLR](https://github.com/zhangkn/KNtoggle-pie)
>
> * nm 旨在浏览mach-o可执行文件中的名称和符号 nm –help
>
>   ```
>    nm -gUj Molon.decrypted |head -n 20
>   ```
>
>   



# Device Tools



> * [cycript](http://www.cycript.org/) 
>
> * [KNAFlexLoader:Tweak.xm,（mac 同系列工具： Reveal)](https://github.com/zhangkn/KNAFlexLoader/blob/master/Tweak.xm)
>
> * plutil
>
> * debugserver :Xcode附带的远程调试工具,它运行在iOS上,可以执行你在lldb(作为客户端)输入的指令同时返回执行结果到lldb上–即所谓的“远程调试”
>
>   ```
>   在设备使用Xcode调试过app之后，debugserver才会被Xcode安装到iOS的“/Developer/usr/bin/”目录下。
>   ```

# See Also 

#### ios8-9

###### Device Tools



>  

> * OpenSSH 
>
> * adv-cmds: 执行 ps 命令报错，需要安装这个工具；
>
> * appsync: 让系统不再验证签名，以免安装应用失败；
>
> * [Cydia Substrate：允许第三方开发者在越狱系统的方法中打一些补丁或扩展方法。](https://kunnan.github.io/2018/07/01/MobileLoader/)
>
> * [zhangkn.github.io](https://zhangkn.github.io/2017/01/iOS_Wifilist/)
>
> * [KNAFlexLoader:Tweak.xm,（mac 同系列工具： Reveal)](https://github.com/zhangkn/KNAFlexLoader/blob/master/Tweak.xm)
>
> * [cycript](http://www.cycript.org/) [frida](https://build.frida.re/frida/)
>
> * [frida-server](https://build.frida.re/frida/)
>
>   
>
>  



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

