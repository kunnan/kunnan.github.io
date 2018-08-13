---
layout: post
title: Network_Layer
date: 2018-05-24
tag: internet
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 揭秘层与层之间的关系
---



# 网络为什么要分层？


>* 复杂的程序都要分层，这是程序设计的要求。
比如，复杂的电商还会分数`据库层、缓存层、Compose 层、Controller 层和接入层`，每一层专注做本层的事情

## OSI Model

| Layer | Name         | Role                                                 | Protocols         | PDU     | Address |
| ----- | ------------ | ---------------------------------------------------- | ----------------- | ------- | ------- |
| 7     | Application  | Initiate contact with the network                    | HTTP, FTP, SMTP   | DATA    |         |
| 6     | Presentation | Format data , optional compression and encapsulation |                   | Data    |         |
| 5     | Session      | Initiates, maintains and tear down session           |                   | Data    |         |
| 4     | Transport    | Transport data                                       | TCP, UDP          | Segment | Port    |
| 3     | Network      | Addressing and routing                               | IP, ICMP (ARP)    | Packet  | IP      |
| 2     | Data link    | Frame formation                                      | Ethernet II (ARP) | Frame   | MAC     |
| 1     | Physical     | Data is transmitter on the media                     |                   | Bits    |         |

 

# 程序是如何工作的？

![image](https://wx4.sinaimg.cn/large/006tBeITgy1frmgpgx22fj30zs0zkdke.jpg)

# 揭秘层与层之间的关系


只要是在网络上跑的包，都是完整的。可以有下层没上层，绝对不可能有上层没下层


>* 一个 HTTP 协议的包经过一个二层设备，二层设备收进去的是整个网络包。这里面 HTTP、TCP、 IP、 MAC 都有。
>```
>1) 什么叫二层设备呀，就是只把 MAC 头摘下来，看看到底是丢弃、转发，还是自己留着。
>2) 那什么叫三层设备呢？就是把 MAC 头摘下来之后，再把 IP 头摘下来，看看到底是丢弃、转发，还是自己留着。
>```



### HTTPS

HTTP + SSL / TLS，也就是在HTTP上又加了一层处理加密信息的模块。服务端和客户端的信息传输都会通过TLS进行加密，所以传输的数据都是加密后的数据，具体流程如下图所示：



![image](https://ws1.sinaimg.cn/large/af39b376gy1fu81w8dt4hj20m10pe41k.jpg)

1. 客户端将自己支持的一套加密规则发送给服务端。  
2. 服务端从中选出一组加密算法与HASH算法，并将自己的身份信息以证书的形式发回给客户端。证书里面包含了服务端地址，加密公钥，以及证书的颁发机构等信息。  
3. 客户端获得服务端证书之后客户端要做以下工作： 
   * a) 验证证书的合法性（颁发证书的机构是否合法，证书中包含的服务端地址是否与正在访问的地址一致等）。
   * b) 如果证书受信任，或者是用户接受了不受信的证书，客户端会生成一串随机数的密码，并用证书中提供的公钥加密。 
   * c) 使用约定好的HASH算法计算握手消息，并使用生成的随机数对消息进行加密，最后将之前生成的所有信息发送给服务端。 

4、服务端接收客户端发来的数据之后要做以下的操作： 

* a) 使用自己的私钥将信息解密取出密码，使用密码解密客户端发来的握手消息，并验证HASH是否与客户端发来的一致。 
* b) 使用密码加密一段握手消息，发送给客户端。 

5、客户端解密并计算握手消息的HASH，如果与服务端发来的HASH一致，此时握手过程结束，之后所有的通信数据将由之前客户端生成的随机密码并利用对称加密算法进行加密。

 

## IPv4 structure

![image](https://ws1.sinaimg.cn/large/af39b376gy1fu81tcrzanj20iz0b3acz.jpg)



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Network_Layer 揭秘层与层之间的关系 -t internet
> #原来""的参数，需要自己加上""
```

