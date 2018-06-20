---
layout: post
title: EnumStringinterrotation
date: 2018-06-20
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 枚举字符串互转，常用语服务端不使用整型枚举的场景
---


# KNConst.h  ` 定义私有的互转函数`

>* 定义互转函数 ` 此文件只有在使用的时候才引用`

```
const NSArray *_knevent_TYPE;


// 创建初始化函数。等于用宏创建一个getter函数

#define NetworkTypeGet (_knevent_TYPE == nil ? _knevent_TYPE = [[NSArray alloc] initWithObjects:\
@"message",\
@"WIFI",\
@"3G",\
@"2G", nil] : _knevent_TYPE)
// 枚举 to 字串
#define eventTypeString(type) ([NetworkTypeGet objectAtIndex:type])
// 字串 to 枚举
#define eventTypeEnum(string) ([NetworkTypeGet indexOfObject:string])
```
# `定义公共的枚举`
>* Const.h `定义公共的枚举`

```
typedef enum {
    event_TYPE_message = 0,
    
    NETWORK_TYPE_WIFI,
    
    NETWORK_TYPE_3G,
    
    NETWORK_TYPE_2G,
    
}event_TYPEenum;
```
#  使用

```
- (NSInteger )respType{
    NSInteger knrespType = eventTypeEnum(self.event);
    return knrespType;
}
```

```
    switch ([tmp respType]) {
        case event_TYPE_message: {
                      
        } break;
            
        case NETWORK_TYPE_WIFI: {
            
            
        } break;
            
        default: {
            
           
            
            
        } break;
    }

```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost EnumStringinterrotation 枚举字符串互转，常用语服务端不使用整型枚举的场景 -t objc
> #原来""的参数，需要自己加上""
```

