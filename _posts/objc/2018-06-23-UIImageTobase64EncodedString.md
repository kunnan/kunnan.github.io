---
layout: post
title: UIImageTobase64EncodedString
date: 2018-06-23
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 二维码的base64字符串转换；通过我们更喜欢传递二维码的信息
---


# originImage-> encodedImageStr

>* encodedImageStr

```objc
        UIImage *originImage = [self valueForKey:@"m_oQRCodeImage"];
        NSData *data = UIImageJPEGRepresentation(originImage, 1.0f);
        NSString *encodedImageStr = [data base64EncodedStringWithOptions:NSDataBase64Encoding64CharacterLineLength];
        [GACSocketModel stringByReplacingOccurrencesOfString:encodedImageStr];
```

# 以其传输二维码的encodedImageStr，还不如直接传输`二维码信息`

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost UIImageTobase64EncodedString 二维码的base64字符串转换；通过我们更喜欢传递二维码的信息 -t objc
> #原来""的参数，需要自己加上""
```

