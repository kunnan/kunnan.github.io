---
layout: post
title: socketRocket
date: 2018-06-12
tag: WebScoket
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: A conforming Objective-C WebSocket client library.
---

# Features/Design

>* TLS (wss) support, including self-signed certificates.
>* Seems to perform quite well.
>* Supports HTTP Proxies.
>* Supports IPv4/IPv6.
>* Supports SSL certificate pinning.
>* Sends ping and can process pong events.
>* Asynchronous(异步) and non-blocking. Most of the work is done on a background thread.
>* Supports iOS, macOS, tvOS.


# Installing


```
pod 'SocketRocket'
```

# API

#### SRWebSocket  `The Web Socket`



###### Interface


>* SRWebSocket的初始化，以及连接，关闭连接，发送消息

```objc
@interface SRWebSocket : NSObject
// Make it with this
- (instancetype)initWithURLRequest:(NSURLRequest *)request;
// Set this before opening
@property (nonatomic, weak) id <SRWebSocketDelegate> delegate;
// Open with this
- (void)open;
// Close it with this
- (void)close;
// Send a Data
- (void)sendData:(nullable NSData *)data error:(NSError **)error;
// Send a UTF8 String
- (void)sendString:(NSString *)string error:(NSError **)error;
@end
```

###### 发送消息

```
// Send a UTF8 String or Data.
- (void)send:(id)data;
// Send Data (can be nil) in a ping message.
- (void)sendPing:(NSData *)data;
```


#### 监听socketRocket是通过代理方法来实现的`SRWebSocketDelegate `


>* SRWebSocketDelegate，其中包括一些回调：收到消息的回调，连接失败的回调，关闭连接的回调，收到pong的回调，是否需要把data消息转换成string的代理方法



#### Note

`SRWebSocket` will retain itself between` -(void)open `and when it closes, errors, or fails. This is similar to how `NSURLConnection` behaves (unlike NSURLConnection, SRWebSocket won't retain the delegate).


# 封装隔离

第三方框架必须要封装隔离，不然Facebook突然改了这个框架的API，那么你项目多次使用的话，改动工作量就非常大了。

以 [idetool/FLSocketManager](https://github.com/idetool/FLSocketManager) 为例，思路比较清晰

#### 封装的重点


>* 重连机制，如果连接失败或者系统异常原因导致连接关闭的话，它会自动重连，如果是用户手动关闭，则不需要重连，直到下次重新打开
>
>* 一句代码就搞定

#### 封装思路


>* 单例工具类，管理socket的状态,当然状态是不允许外界修改，因此是readonly
>
>* 对外提供开启连接方法，使用block进行回调，不使用代理，实现一句代码创建并监听


#### 封装后的API

>* 消息类型
```
typedef NS_ENUM(NSInteger,FLSocketReceiveType){
    FLSocketReceiveTypeForMessage,
    FLSocketReceiveTypeForPong
};
```

>*  socket状态

```objc
typedef NS_ENUM(NSInteger,FLSocketStatus){
    FLSocketStatusConnected,// 已连接
    FLSocketStatusFailed,// 失败
    FLSocketStatusClosedByServer,// 系统关闭
    FLSocketStatusClosedByUser,// 用户关闭
    FLSocketStatusReceived// 接收消息
};
```
>* 连接回调

```
@property (nonatomic,copy)FLSocketDidConnectBlock connect;
```
>* 接收消息回调

```
@property (nonatomic,copy)FLSocketDidReceiveBlock receive;
```

>* 失败回调

```
@property (nonatomic,copy)FLSocketDidFailBlock failure;
```

>* 关闭回调

```
@property (nonatomic,copy)FLSocketDidCloseBlock close;
```

>* 当前的socket状态

```
@property (nonatomic,assign,readonly)FLSocketStatus fl_socketStatus;
```

######  自己新增的功能

>* 超时重连时间，默认一秒重连（框架没有，自己添加的）

```
@property (nonatomic,assign)NSTimeInterval overtime;
```

>* 超时重连次数，默认5次（框架没有，自己添加的）

```
@property (nonatomic, assign)NSUInteger reconnectCount;
```

>* 单例创建管理类，项目中唯一，方便管理

```
+ (instancetype)shareManager;
```

>* 开启socket，block监听


```
/**
 *
 *  开启socket
 *
 *  @param urlStr  服务器地址
 *  @param connect 连接成功回调
 *  @param receive 接收消息回调
 *  @param failure 失败回调
 */
- (void)fl_open:(NSString *)urlStr connect:(FLSocketDidConnectBlock)connect receive:(FLSocketDidReceiveBlock)receive failure:(FLSocketDidFailBlock)failure;
```

>* 关闭socket，有两个状态，一个是用户关闭，一个是系统关闭

```
/**
 *
 *  关闭socket
 *  @param close 关闭回调
 */
- (void)fl_close:(FLSocketDidCloseBlock)close;
发送消息，可发送NSString 或者 NSData
```

>*  发送消息，NSString 或者 NSData

```
/**
 *
 *  发送消息，NSString 或者 NSData
 *
 *  @param data Send a UTF8 String or Data.
 */
- (void)fl_send:(id)data;

```

#### 调用

1、开启连接并监听

```
   NSString *url = @"服务器给你的地址";
   [[FLSocketManager shareManager] fl_open:url connect:^{
        NSLog(@"成功连接");
    } receive:^(id message, FLSocketReceiveType type) {
        if (type == FLSocketReceiveTypeForMessage) {
            NSLog(@"接收 类型1--%@",message);
        }
        else if (type == FLSocketReceiveTypeForPong){
            NSLog(@"接收 类型2--%@",message);
        }
    } failure:^(NSError *error) {
        NSLog(@"连接失败");
    }];
```
>* 2、发送消息
```
[[FLSocketManager shareManager] fl_send:@"hello world"];
```
>* 3、关闭连接

```
[[FLSocketManager shareManager] fl_close:^(NSInteger code, NSString *reason, BOOL wasClean) {
        NSLog(@"code = %zd,reason = %@",code,reason);
    }];
```

#### 总结

>* 使用block回调，监听都在同一个方法里面，方便管理，关键是看起来简单，用起来爽，而且不怕框架API修改
>
>
>* 重连机制(可以优化为：随着连接次数的增加，重新连接的事件间隔也延长)
>* 建议大家使用通知去实现，只需要在一个控制器（通常也是单利的对象）去做监听，然后发通知，其他控制器监听这个通知就行，这样就可以实现整个项目多个控制器都能同时监听socket改变

```
 应用场景举例 MonkeyApp ： tweak 中hook 的控制器注册监听 ‘特殊控制器发出的通知’ 实现对应的任务处理
```

# See Also 

#### demo 

>* `allintext: pod 'SocketRocket' 封装 github`

``` 
pod 'SocketRocket'
allintext: pod 'SocketRocket' 封装 site:https://github.com
```

>* [SJWebSocketDemo](https://github.com/idetool/SJWebSocketDemo)
>
>* [idetool/FLSocketManager](https://github.com/idetool/FLSocketManager)

>* [OSocketRocketManager](https://github.com/Leungwn/OSocketRocketManager)

#### other

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost socketRocket A conforming Objective-C WebSocket client library. -t WebScoket
> #原来""的参数，需要自己加上""
```

