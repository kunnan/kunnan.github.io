---
layout: post
title: Regular_expressions_that_are_commonly_used
date: 2018-08-08
tag: RegularExpressions
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 常用的正则表达式
---



# 例子



#### `^(@\S+\s+)+飞机$ `



> * 判断m_nsContent  字符串中是否命中正则表达式的例子
>
>   
>
>   ```objc
>           NSRange range = [m_nsContent rangeOfString:[WLSession shareSession].kick_rule options:NSRegularExpressionSearch];//^1[3]\\d{9}$
>           if (range.location != NSNotFound) {
>               // 找到了就进行上报
>               NSLog(@" 命中了 kick_rule：%@  m_nsContent  %@",[WLSession shareSession].kick_rule,[m_nsContent substringWithRange:range]);
>               [self reportGroupkick_ruleAtUserListWithMsgWrap:wrap];
>           }
>   
>   ```
>
>   ```
>   \"message\":\"@姬平05290 @zhangkn.github.io @kunnan.github.io 飞机\"
>   ```
>
>   



# See Also 

>*  查看中文
>
>  ```
>   echo -e "^(@\\S+\\s+)+\xe9\xa3\x9e\xe6\x9c\xba$" 
>  ```
>
>  * 或者使用chrome的console.log\ decode
>
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost Regular_expressions_that_are_commonly_used 常用的正则表达式 -t RegularExpressions
> #原来""的参数，需要自己加上""
```

