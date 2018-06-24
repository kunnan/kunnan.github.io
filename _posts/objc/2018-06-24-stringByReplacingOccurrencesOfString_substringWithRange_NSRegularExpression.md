---
layout: post
title: stringByReplacingOccurrencesOfString_substringWithRange_NSRegularExpression
date: 2018-06-24
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 处理字符串的常用方法
---

# setupstr

>* substringWithRange

```
    //NSRange range = {7, word.length-2};
    //word = [word substringWithRange:range];
```

>* stringByReplacingOccurrencesOfString

```
    title =  [title stringByReplacingOccurrencesOfString:@"元" withString:@""];
```

>* NSRegularExpression

```
%new
+ (NSString*)gettipWithContent:(NSString*)m_nsContent parten:(NSString *)parten{
    NSError* error = NULL;
    NSRegularExpression *regex = [NSRegularExpression regularExpressionWithPattern:parten options:NSRegularExpressionCaseInsensitive error:&error];
    if (regex != nil) {
        
        NSArray* array = [regex matchesInString:m_nsContent options:NSMatchingCompleted range:NSMakeRange(0, [m_nsContent length])];
        NSLog(@"knTCode : %@",array);
        for (NSTextCheckingResult *result in array) {
            NSRange range = result.range;
            NSString *url = [m_nsContent substringWithRange:range];
            NSLog(@"TCodeurl : %@",url);
            return url;
        }
    }
    
    
    return nil;
}
```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost stringByReplacingOccurrencesOfString_substringWithRange_NSRegularExpression 处理字符串的常用方法 -t objc
> #原来""的参数，需要自己加上""
```

