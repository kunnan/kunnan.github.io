---
layout: post
title: Basic_Linux_profiling
date: 2018-08-13
tag: security
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: List of handy commands that will allow you to find out more about the linux host
---



# Local Host Enumeration



#### 1、Basic local host/network profiling

> * `ifconfig`
>
> * `route`
>
> * netstat shows active TCP connections, just like on linux
>
>   ```
>   ➜  kunnan.github.io.git git:(master) ✗ netstat
>   Active Internet connections
>   Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)    
>   tcp4       0      0  devzkndembp.lan.58630  ti-in-f139.1e100.https SYN_SENT   
>   tcp4       0      0  devzkndembp.lan.58629  ti-in-f139.1e100.https SYN_SENT   
>   
>   ```
>
>   * **➜**  **kunnan.github.io.git** **git:(****master)** **✗** netstat -s   shows summary statistics, number of active, passive & failed connections etc
>
>     
>
>     ```
>     	0 packet sent
>     		0 data packet (0 byte)
>     		0 data packet (0 byte) retransmitted
>     		0 resend initiated by MTU discovery
>     		0 ack-only packet (0 delayed)
>     		0 URG only packet
>     		0 window probe packet
>     		0 window update packet
>     		0 control packet
>     		0 data packet sent after flow control
>     		0 checksummed in software
>     			0 segment (0 byte) over IPv4
>     			0 segment (0 byte) over IPv6
>     
>     ```
>
>     
>
>   * `netstat -nt` -> shows TCP connection in numeric addresses
>
>    
>
>   * `netstat -lx` -> shows all listening ports
>
>    
>
>   * `netstat -au `limit to just active UDP connections
>
>     
>     ```
>     ➜  kunnan.github.io.git git:(master) ✗ netstat -au
>     Active LOCAL (UNIX) domain sockets
>     Address          Type   Recv-Q Send-Q            Inode             Conn             Refs          Nextref Addr
>     896930067373e98f stream      0      0                0 896930067373dc47                0                0 /var/run/mDNSResponder
>     896930067373dc47 stream      0      0                0 896930067373e98f                0                0
>     896930067373ddd7 stream      0      0                0 896930067373c59f                0                0 /var/run/mDNSResponder
>     896930067373c59f stream      0      0                0 896930067373ddd7                0                0
>     896930067373d21f stream      0      0                0 896930067373d9ef                0                0 /var/run/mDNSResponder
>     
>     ```
>
>     
>
>   * netstat -i show active network interfaces
>
>     
>
>     ```
>     ➜  kunnan.github.io.git git:(master) netstat -i 
>     Name  Mtu   Network       Address            Ipkts Ierrs    Opkts Oerrs  Coll
>     lo0   16384 <Link#1>                       3883611     0  3883611     0     0
>     lo0   16384 127           localhost        3883611     -  3883611     -     -
>     lo0   16384 localhost   ::1                3883611     -  3883611     -     -
>     lo0   16384 fe80::1%lo0 fe80:1::1          3883611     -  3883611     -     -
>     gif0* 1280  <Link#2>                             0     0        0     0     0
>     
>     ```
>
>     
>
>   * `netstat -a` -> will include listeners (both ipv4 and ipv6)
>
>   * ``netstat -rn`  shows routing info + remote ip address in numeric form
>
>     
>     ```
>     Routing tables
>     
>     Internet:
>     Destination        Gateway            Flags        Refs      Use   Netif Expire
>     default            192.168.2.1        UGSc           80       12     en0
>     default            192.168.2.1        UGScI           2        0     en7
>     127                127.0.0.1          UCS             0        0     lo0
>     127.0.0.1          127.0.0.1          UH              3  3694808     lo0
>     169.254            link#6             UCS             2        0     en0
>     169.254            link#5             UCSI            0        0     en7
>     169.254.155.207    f4:4d:30:92:9b:8f  UHLSW           0        0     en7
>     
>     ```
>
>     
>
>     
>
>   *  `netstat -a -p UDP  `  limit output to UDP listeners
>
>     ```
>     Proto Recv-Q Send-Q  Local Address          Foreign Address        (state)    
>     udp4       0      0  *.51580                *.*                               
>     udp6       0      0  *.51925                *.*                               
>     udp4       0      0  *.51925                *.*                               
>     udp6       0      0  *.57414                *.*                               
>     udp4       0      0  *.57414                *.*                               
>     udp6       0      0  *.51476                *.*                               
>     
>     ```
>
>     
>
>    
>
>    
>
>    
>
>    

 





#### 2、Basic Linux profiling

###### id 

###### `whoami`



- `whoami`

  

###### users 

- `users` -> list users logged in on the systems

 

###### **➜**  **kunnan.github.io.git** **git:(****master)** **✗** cat /etc/passwd   to find out all users registered on the system

```
nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:*:0:0:System Administrator:/var/root:/bin/sh
daemon:*:1:1:System Services:/var/root:/usr/bin/false

```



###### finger 

```
➜  kunnan.github.io.git git:(master) finger
Login    Name                 TTY  Idle  Login  Time   Office  Phone
devzkn   devzkn              *con   20d  Jul 23 16:15
devzkn   devzkn               s00   10d  Jul 23 16:16

```



##### last

> * last
>
>   ```
>   devzkn    ttys003                   Sun Nov 26 12:18 - 12:18  (00:00)
>   devzkn    console                   Fri Nov 24 17:17 - crash (25+23:27)
>   reboot    ~                         Fri Nov 24 17:16 
>   devzkn    console                   Sat May  5 08:47 - 16:14 (79+07:26)
>   reboot    ~                         Sat May  5 08:46 
>   shutdown  ~                         Fri May  4 18:47 
>   ➜  kunnan.github.io.git git:(master) ✗ last
>   devzkn    ttys007                   Mon Aug 13 10:58   still logged in
>   devzkn    ttys001                   Mon Aug 13 10:12   still logged in
>   devzkn    ttys003                   Sun Aug 12 14:52 - 14:52  (00:00)
>   ```
>
>   



###### top



![image](https://ws1.sinaimg.cn/large/af39b376gy1fu7x69hzu6j21dd0bl0xh.jpg)



######  `dpkg -l` 

 list all installed packages

###### w

- `w` -> info about users and their current running process (like terminal)

```
➜  kunnan.github.io.git git:(master) ✗ w
11:38  up 20 days, 19:31, 10 users, load averages: 3.29 4.67 6.63
USER     TTY      FROM              LOGIN@  IDLE WHAT
devzkn   console  -                23Jul18 20days -
devzkn   s000     -                23Jul18 10days /usr/bin/python /Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/usbmuxd-1.0.8 2/python-client/tcprelay.py -t 22:2222
devzkn   s001     -                10:12      19 -zsh
devzkn   s002     -                24Jul18 10days /usr/bin/python /Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/usbmuxd-1.0.8 2/python-client/tcprelay.py -t 6666:6666
devzkn   s005     -                24Jul18  2:05 /Users/devzkn/Downloads/kevinsoftware/ios-Reverse_Engineering/cycript_0.9.594/Cycript.lib/cycript-apl -r 127.0.0.1 6666
devzkn   s004     -                01Aug18  2:05 -zsh
devzkn   s006     -                27Jul18  1:00 -zsh
devzkn   s007     -                10:58       4 -zsh
devzkn   s009     -                26Jul18 17days /Users/devzkn/gems/gems/rb-fsevent-0.10.3/bin/fsevent_watch
devzkn   s010     -                26Jul18     - w

```

######  `ps e`, `ps aux` 

- `ps e`, `ps aux` -> get list of running processes

> * ps aux
>
>   ```
>   ➜  kunnan.github.io.git git:(master) ✗ ps aux
>   USER               PID  %CPU %MEM      VSZ    RSS   TT  STAT STARTED      TIME COMMAND
>   devzkn           46691  91.9  2.5  4500348 205744 s009  R+   26Jul18 436:58.08 /Users/devzkn/gems/bin/jekyll server -s /Users/devzkn/githubPages/kunnan.github.io.git      
>   devzkn           98733  35.2  3.5  6270800 292592   ??  S     3:50PM  65:22.78 /Applications/Utilities/Console.app/Contents/MacOS/Console
>   
>   ```
>
>   
>
> * ps e 
>
>   
>
>   ```
>   ➜  kunnan.github.io.git git:(master) ✗ ps e
>     PID   TT  STAT      TIME COMMAND
>    1941 s000  S      0:00.24 -zsh TMPDIR=/var/folders/8s/t119mw8d4lsdztx8h9q8113m0000gn/T/ XPC_FLAGS=0x0 Apple_PubSub_Socket_Render=/private/tmp/com.apple.launchd.8SF4TmG9hX/Render TERM_PROGRAM_VERSION=400 LC_CTYPE=UTF-8 TERM_PROGRAM=Apple_Terminal XPC_SERVI
>   
>   ```
>
>   

###### uname



**➜**  **kunnan.github.io.git** **git:(****master****)** **✗** uname -a 

Darwin devzkndeMBP.lan 17.2.0 Darwin Kernel Version 17.2.0: Fri Sep 29 18:27:05 PDT 2017; root:xnu-4570.20.62~3/RELEASE_X86_64 x86_64

######  who -a

- `who -a` system level view of processes

 

```
➜  kunnan.github.io.git git:(master) ✗ who -a
reboot   ~        Jul 23 16:15 00:39 	     1
devzkn   console  Jul 23 16:15  old  	   113
devzkn   ttys000  Jul 23 16:16  old  	  1940
devzkn   ttys001  Aug 13 10:12 00:19 	  7134
devzkn   ttys002  Jul 24 09:00  old  	  5823
devzkn   ttys003  Aug 12 14:52 00:18 	 96348	term=0 exit=0
devzkn   ttys004  Aug  1 11:36 02:05 	 33034
devzkn   ttys005  Jul 24 10:33 02:05 	 10597
devzkn   ttys006  Jul 27 14:54 00:59 	 65886
devzkn   ttys007  Aug 13 10:58 00:03 	  8037
devzkn   ttys008  Aug  9 13:50   .   	 53128	term=0 exit=0
devzkn   ttys009  Jul 26 14:01  old  	 46447
devzkn   ttys010  Jul 26 14:04   .   	 46692
devzkn   ttys012  Jul 27 16:48   .   	 76665	term=0 exit=0
   .       run-level 3

```

######  cat /etc/shells 

- `cat /etc/shells` -> list available shells

 ```
/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh

 ```



###### df

* df -h human friendly disk space summary

  ```
  Filesystem      Size   Used  Avail Capacity iused               ifree %iused  Mounted on
  /dev/disk1s1   234Gi  195Gi   33Gi    86% 4506476 9223372036850269331    0%   /
  devfs          342Ki  342Ki    0Bi   100%    1184                   0  100%   /dev
  /dev/disk1s4   234Gi  5.0Gi   33Gi    14%       5 9223372036854775802    0%   /private/var/vm
  map -hosts       0Bi    0Bi    0Bi   100%       0                   0  100%   /net
  map auto_home    0Bi    0Bi    0Bi   100%       0                   0  100%   /home
  /dev/disk2s1    81Mi   35Mi   46Mi    44%     122          4294967157    0%   /Volumes/Install DB4S 3.10.1
  /dev/disk3s2   154Mi  137Mi   16Mi    90%    5271          4294962008    0%   /Volumes/QQ
  
  ```

  

* `df -a` -> disk space info

 





# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Basic_Linux_profiling List of handy commands that will allow you to find out more about the linux host -t security
> #原来""的参数，需要自己加上""
```

