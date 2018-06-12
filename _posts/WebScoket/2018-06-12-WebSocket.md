---
layout: post
title: WebSocket
date: 2018-06-12
tag: WebScoket
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 一种网络通信协议,提供全双工通信信道,以基于事件的方式，赋予浏览器实时通信能力;协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。
---



# I 、简介

WebSocket 协议在2008年诞生，2011年成为国际标准。所有浏览器都已经支持了。

特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于[服务器推送技术](https://en.wikipedia.org/wiki/Push_technology)的一种。
![image](https://ws2.sinaimg.cn/large/af39b376gy1fs85o3sbesj20hg0e7q4v.jpg)

#### 1.1特点

>* （1）建立在 TCP 协议之上，服务器端的实现比较容易。

>* （2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

>* （3）数据格式比较轻量，性能开销小，通信高效。

>* （4）可以发送文本，也可以发送二进制数据。（`blob对象或Arraybuffer对象`）

>* （5）没有同源限制，客户端可以与任意服务器通信。

>* （6）协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。


![image](https://ws2.sinaimg.cn/large/af39b376gy1fs85l6zx0kj20bm08ogo0.jpg)


# II、[Socket](https://en.wikipedia.org/wiki/Network_socket) 与 [WebScoket](http://www.ruanyifeng.com/blog/2017/05/websocket.html)

Socket 其实并不是一个协议。它工作在 OSI 模型会话层（第5层），是为了方便大家直接使用更底层协议（一般是 TCP 或 UDP ）而存在的一个抽象层。
![image](https://ws2.sinaimg.cn/large/af39b376gy1fs85kbjwcdj20f50dadhe.jpg)
而 [WebSocket](http://www.websocket.org/) 则不同，它是一个完整的 [应用层协议](https://datatracker.ietf.org/doc/rfc6455/)，包含一套标准的 [API](https://html.spec.whatwg.org/multipage/web-sockets.html#network) 。

所以，从使用上来说，WebSocket 更易用，而 Socket 更灵活。


#### 2.1 代表框架

>* 基于Scoket原生：代表框架 CocoaAsyncSocket。
>* 基于WebScoket：代表框架 SocketRocket。
>* 基于MQTT：代表框架 MQTTKit。
>* 基于XMPP：代表框架 XMPPFramework。


#### 2.2 [客户端的 API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)


#### 2.3 服务端的实现

###### 2.3.1 常用的 Node 实现有以下三种

>* µWebSockets
>* Socket.IO
>* WebSocket-Node
>
>* 一款非常特别的 WebSocket 服务器：[Websocketd](http://websocketd.com/)




# See Also 
>* [WebSocket 教程](http://www.ruanyifeng.com/blog/2017/05/websocket.html?utm_source=tuicool&utm_medium=referral)

>* [WebSocket 在线测试](http://www.blue-zero.com/WebSocket/)

>* [WebSocket wiki](https://en.wikipedia.org/wiki/WebSocket)


>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost WebSocket 一种网络通信协议,提供全双工通信信道,以基于事件的方式，赋予浏览器实时通信能力;协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。 -t WebScoket
> #原来""的参数，需要自己加上""
```

