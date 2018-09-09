---
layout: post
title: addObserver_forKeyPath_options_options
date: 2018-09-07
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: An instance 0x18a02a00 of class WCNewCommitViewController was deallocated while key value observers were still registered with it.
---





#  removeObserver



> * q
>
>   ```
>   Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'An instance 0x18a02a00 of class WCNewCommitViewController was deallocated while key value observers were still registered with it. Current observation info: <NSKeyValueObservationInfo 0x1a59baa0> ( <NSKeyValueObservance 0x19047eb0: Observer: 0x18a02a00, Key path: navigationItem.leftBarButtonItem, Options: <New: YES, Old: NO, Prior: NO> Context: 0x0, Property: 0x191df760> )'
>   ```
>
> * a
>
>   ```
>           [WCNewCommit removeObserver:WCNewCommit forKeyPath:@"navigationItem.leftBarButtonItem" context:nil];//  Options <New: YES, Old: NO, Prior: NO>   Context: 0x0, Property: 0x180bb480> , Property: 0x19fe2e20>
>   
>           [WCNewCommit removeObserver:WCNewCommit forKeyPath:@"navigationItem.rightBarButtonItem" context:nil];//  Options <New: YES, Old: NO, Prior: NO>   Context: 0x0, Property: 0x180bb480> , Property: 0x19fe2e20>
>   
>   ```
>
>   ![image](https://wx4.sinaimg.cn/large/af39b376gy1fv1ag1js61j212q0f3gsf.jpg)

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost addObserver_forKeyPath_options_options Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'An instance 0x18a02a00 of class WCNewCommitViewController was deallocated while key value observers were still registered with it. Current observation info: <NSKeyValueObservationInfo 0x1a59baa0> ( <NSKeyValueObservance 0x19047eb0: Observer: 0x18a02a00, Key path: navigationItem.leftBarButtonItem, Options: <New: YES, Old: NO, Prior: NO> Context: 0x0, Property: 0x191df760> )' -t objc
> #原来""的参数，需要自己加上""
```

