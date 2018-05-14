---
layout: post
title: gitmodules
date: 2018-05-14
tag: Git
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: submodules a URL is specified which can be used for cloning the submodules.
---

# 前言

gitmodules - defining submodule properties


# SYNOPSIS



>* `$GIT_WORK_DIR/.gitmodules`
>```
>devzkndeMacBook-Pro:UIDeviceCrasher devzkn$ tree -La 1
.
├── .git
├── .gitignore
├── .gitmodules
├── Makefile
├── UIDeviceCrasher.x
├── framework
└── layout
>```


# DESCRIPTION

The .gitmodules file, located in the top-level directory of a Git working tree, is a text file with a syntax matching the requirements of [git-config[1]](https://git-scm.com/docs/git-config).




# EXAMPLES

>*  [.gitmodules](https://github.com/kunnan/UIDeviceCrasher)
><script src="https://gist.github.com/zhangkn/16af252f79ce4f4a9a645f7576ac1edd.js"></script>


>* [UIDeviceCrasher/Makefile](https://github.com/kunnan/UIDeviceCrasher/blob/master/Makefile)
>```
>ifeq ($(shell [ -f ./framework/makefiles/common.mk ] && echo 1 || echo 0),0)
all clean package install::
	git submodule update --init
	./framework/git-submodule-recur.sh init
	$(MAKE) $(MAKEFLAGS) MAKELEVEL=0 $@
else
SDKVERSION = 5.1
INCLUDE_SDKVERSION = 6.1
TARGET_IPHONEOS_DEPLOYMENT_VERSION = 3.0
TWEAK_NAME = AAAUIDeviceCrasher
AAAUIDeviceCrasher_FILES = UIDeviceCrasher.x
AAAUIDeviceCrasher_LIBRARIES = substrate
AAAUIDeviceCrasher_FRAMEWORKS = Foundation UIKit
include framework/makefiles/common.mk
include framework/makefiles/tweak.mk
endif
>```
>

>* make package 
>```
>devzkndeMacBook-Pro:UIDeviceCrasher devzkn$ make package 
git submodule update --init
Submodule 'framework' (git://github.com/rpetrich/theos.git) registered for path 'framework'
Cloning into '/Users/devzkn/code/iosre/UIDeviceCrasher/framework'...
Submodule path 'framework': checked out '17afcc0c54be60db7caa1d1fe8473d4d6e937e83'
./framework/git-submodule-recur.sh init
Entering 'framework'
Submodule 'include' (git://github.com/rpetrich/iphoneheaders.git) registered for path 'include'
Cloning into '/Users/devzkn/code/iosre/UIDeviceCrasher/framework/include'...
Submodule path 'include': checked out '0cecb51e76d29486415988e96886cff2c97d594f'
Entering 'include'
Submodule 'CaptainHook' (git://github.com/rpetrich/CaptainHook.git) registered for path 'CaptainHook'
Cloning into '/Users/devzkn/code/iosre/UIDeviceCrasher/framework/include/CaptainHook'...
Submodule path 'CaptainHook': checked out '1efb1773e014acf2989f9874edce141f4f2d40d5'
Entering 'CaptainHook'
/Applications/Xcode.app/Contents/Developer/usr/bin/make  MAKELEVEL=0 package
>```


>* .gitignore
>```
>devzkndeMacBook-Pro:UIDeviceCrasher devzkn$ cat .gitignore
._*
*.deb
.debmake
_
obj
.theos
.DS_Store
>```

# See Also 

>* [gitmodules](https://git-scm.com/docs/gitmodules)
>

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost gitmodules submodules a URL is specified which can be used for cloning the submodules. -t Git
> #原来""的参数，需要自己加上""
```

