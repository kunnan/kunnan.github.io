---
layout: post
title: dismissViewControllerAnimatedUIAlertController
date: 2018-05-15
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: UIIAlertController的自动消失
---

# 消除 sb 的UIAlertController

>* hook sb
>```plist
>{ Filter = { Bundles = ( "com.tencent.xin","com.apple.springboard","com.apple.Preferences"); }; }
>```
>
>* hook all app 
>```
>com.apple.UIKit
>```
>
>*code
><script src="https://gist.github.com/zhangkn/8bd6e511ef5d03442083f4cdd91e3b9f.js"></script>



# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost dismissViewControllerAnimatedUIAlertController UIIAlertController的自动消失 -t iosre
> #原来""的参数，需要自己加上""
```

