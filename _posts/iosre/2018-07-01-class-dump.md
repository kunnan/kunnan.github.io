---
layout: post
title: class-dump
date: 2018-07-01
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Generate Objective-C headers from Mach-O files
---



# [ 入口文件:class-dump/**class-dump.m**](https://github.com/kunnan/class-dump/blob/master/class-dump.m)





# usage



> 
>
> - [可以dump混编的](https://github.com/AloneMonkey/MonkeyDev/tree/master/bin)
>
>   * `class-dump  --arch arm64  AppStore -H -o -A ./header`
>     * 加上-A可以显示方法在文件中的实现地址
>   * `[class-dump](https://github.com/AloneMonkey/MonkeyDev/blob/master/bin/class-dump)` 
>
>   ```
>   devzkndeMBP:bin devzkn$ swiftOCclass-dump  --arch arm64 /Users/devzkn/decrypted/AppStoreV10.2/Payload/AppStore.app/AppStore -H -o  /Users/devzkn/decrypted/AppStoreV10.2/head
>   ```

 



# See Also 

>* [class-dump:源码，alonemonkey进行注解](https://github.com/kunnan/class-dump)
>* [class-dump:利用 Objective-C 语言的 runtime 特性，将存储在 Mach-O 文件中的 @interface 和 @protocol 信息提取出来，并生成相应的 .h 文件](https://github.com/zhangkn/MonkeyDev/blob/master/bin/class-dump)
>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost class-dump Generate Objective-C headers from Mach-O files -t iosre
> #原来""的参数，需要自己加上""
```

