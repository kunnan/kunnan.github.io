---
layout: post
title: code_Signature
date: 2018-07-29
tag: dyld
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Mach-O文件的签名信息
---



# 使用jtool  获取sig\entitlements 

> * **➜**  **~** jtool -arch arm64 -v --sig tmp.arm64 
>
>   ```
>   ➜  ~ jtool -arch arm64 -v --sig tmp.arm64
>   Blob at offset: 480800 (16192 bytes) is an embedded signature of 11820 bytes, and 5 blobs
>   	Blob 0: Type: 0 @52: Code Directory (2534 bytes)
>   		Version:     20200
>   		Flags:       none (0x0)
>   		CodeLimit:   0x75620
>   ```
>
>   
>
> * jtool -arch arm64 --ent tmp.arm64  
>
>   ```
>   ➜  ~ jtool -arch arm64 --ent tmp_64.dylib
>   tmp_64.dylib apparently does not contain any entitlements
>   ➜  ~ jtool -arch arm64 --ent tmp.arm64   
>   <?xml version="1.0" encoding="UTF-8"?>
>   ```
>
>   

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin//knpost code_Signature Mach-O文件的签名信息 -t dyld
> #原来""的参数，需要自己加上""
```

