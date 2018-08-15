---
layout: post
title: Keychain-Dumper
date: 2018-05-30
tag: iosre
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: A tool to check which keychain items are available to an attacker once an iOS device has been jailbroken
---


# Keychain-Dumper

>*  [keychain dumper 的优化版本](https://github.com/zhangkn/KCdumper)
>
>* [Keychain-Dumper](https://github.com/zhangkn/Keychain-Dumper)

```
Usage: keychain_dumper [-e]|[-h]|[-agnick]
<no flags>: Dump Password Keychain Items (Generic Password, Internet Passwords)
-a: Dump All Keychain Items (Generic Passwords, Internet Passwords, Identities, Certificates, and Keys)
-e: Dump Entitlements
-g: Dump Generic Passwords
-n: Dump Internet Passwords
-i: Dump Identities
-c: Dump Certificates
-k: Dump Keys
```
# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Keychain-Dumper A tool to check which keychain items are available to an attacker once an iOS device has been jailbroken -t iosre
> #原来""的参数，需要自己加上""
```

