---
layout:     post
title:      yy_modelWithJSON & JSONModel
subtitle:   yy_modelWithJSON:json
date:       2016-10-26
author:     
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - iOS
    - 开发技巧
---


# 前言



>* 通过[YYModel](https://github.com/ibireme/YYModel)这个框架安全快速的完成JSON到模型的转换，其中还会介绍到一款好用的插件[ESJsonFormat](https://github.com/EnjoySR/ESJsonFormat-Xcode)。

>* JSONModel 这个框架,能够完成模型到JSON的转换


# I、YYModel

#### 1、1 首先创建模型类

创建模型类我们可以通过[ESJsonFormat](https://github.com/EnjoySR/ESJsonFormat-Xcode)这款插件快速完成。

>* [将JSON格式化输出为模型的属性 :](https://github.com/EnjoySR/ESJsonFormat-Xcode)

>* [Xcode 8及之后使用插件见](http://www.cocoachina.com/ios/20161207/18313.html)
>



#### 1.2、使用YYModel进行解析


# II、JSONModel


#### 2.1 `@interface GACSocketModel : JSONModel`

>* GACSocketModel.h
>

```
#import <JSONModel/JSONModel.h>

@interface GACSocketModel : JSONModel
@property (nonatomic, copy) NSString<Optional> *event;
@property (nonatomic, copy) NSDictionary<Optional> *data;
@property (nonatomic, copy) NSString<Optional> *type;
```


>* GACSocketModel.m
>

```
#import "GACSocketModel.h"

@implementation GACSocketModel

- (NSString *)socketModelToJSONString {
    NSAssert(self.data != nil, @"Argument must be non-nil");
    if (![self.data isKindOfClass:[NSDictionary class]]) {
        return nil;
    }
    NSString *bodyString = [self dictionnaryObjectToString:self.data];
    self.data = (NSDictionary *)bodyString;
    NSString *jsonString = [self toJSONString];
    jsonString = [jsonString stringByAppendingString:@"\r\n"];
    return jsonString;
}

- (NSString *)dictionnaryObjectToString:(NSDictionary *)object {
    NSError *error = nil;
    NSData *stringData =
    [NSJSONSerialization dataWithJSONObject:object options:NSJSONWritingPrettyPrinted error:&error];
    if (error) {
        return nil;
    }
    
    NSString *jsonString = [[NSString alloc] initWithData:stringData encoding:NSUTF8StringEncoding];
    // 字典对象用系统JSON序列化之后的data，转UTF-8后的jsonString里面会包含"\n"及" "，需要替换掉
    jsonString = [jsonString stringByReplacingOccurrencesOfString:@"\n" withString:@""];
    jsonString = [jsonString stringByReplacingOccurrencesOfString:@" " withString:@""];
    return jsonString;
}
```

#### 2.2 效果

```
 发送中。。。data：{"type":"text","data":"{\"text\":\"我\"}","fromwxid":"","fromwxnick":"","event":"message"}
```