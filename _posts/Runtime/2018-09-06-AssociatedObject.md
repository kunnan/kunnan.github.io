---
layout: post
title: AssociatedObject
date: 2018-09-06
tag: Runtime
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 动态创建属性：使用分类、@dynamic、objc_setAssociatedObject、objc_getAssociatedObject 进行实现
---

# 前言

利用属性进行传值的时候，我们就可以利用本文的方法进行动态创建属性。尤其在逆向其他app的时候，往已经存在class新增一个属性，用于数据传递，尤其是异步操作的时候。



# code

#### WCNewCommitViewController+KNWCNewCommitViewControllerAssociatedObject.h

> * code
>
>   ```
>   //  Created by devzkn on 06/09/2018.
>   //  Copyright © 2018 devzkn. All rights reserved.
>   //
>   
>   #import "WCNewCommitViewController.h"
>   
>   @interface NSObject (KNWCNewCommitViewControllerAssociatedObject)
>   //    isa (Class): NSKVONotifying_WCNewCommitViewController (isa, 0x5a10db2abf7)
>   @property (nonatomic, strong) id associatedObject;
>   @end
>   
>   ```
>

#### WCNewCommitViewController+KNWCNewCommitViewControllerAssociatedObject.m

> * code
>
>   ```
>   //  Created by devzkn on 06/09/2018.
>   //  Copyright © 2018 devzkn. All rights reserved.
>   //
>   
>   #import "WCNewCommitViewController+KNWCNewCommitViewControllerAssociatedObject.h"
>   
>   @implementation NSObject (KNWCNewCommitViewControllerAssociatedObject)
>   
>   @dynamic associatedObject;
>   - (void)setAssociatedObject:(id)object {
>       objc_setAssociatedObject(self,
>                                @selector(associatedObject), object,
>                                OBJC_ASSOCIATION_RETAIN_NONATOMIC);
>   }
>   - (id)associatedObject {
>       return objc_getAssociatedObject(self,
>                                       @selector(associatedObject));
>   }
>   
>   @end
>   
>   ```
>

#### 效果



> * usage: `#import "WCNewCommitViewController+KNWCNewCommitViewControllerAssociatedObject.h"`
>
>   ```
>       [WCNewCommit setAssociatedObject:@"sssss"];
>   
>   ```
>
> * ret
>
>   ```
>           NSLog(@"associatedObject:%@",[self valueForKey:@"associatedObject"]);//2018-09-06 12:06:06.977711 WeChat[717:226743] associatedObject:sssss
>   
>   ```
>

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost AssociatedObject 动态创建属性：使用分类、@dynamic、objc_setAssociatedObject、objc_getAssociatedObject 进行实现 -t Runtime
> #原来""的参数，需要自己加上""
```

