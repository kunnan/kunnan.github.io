---
layout: post
title: Python_brief_introduction
date: 2018-05-31
tag: py
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: Python 简介
---

# [The Python programming language](https://www.python.org/)



>* pyenv 

```
devzkndeMacBook-Pro:iOS-private-api-checker devzkn$ pyenv versions
  system
  3.2
* 3.6.5 (set by /Users/devzkn/code/iosre/iOS-private-api-checker/.python-version)
  3.7.0b2
```
>* `Python`会安装在 /usr/local/bin 目录中，`Python库`安装在/usr/local/lib/pythonXX，XX为你使用的Python的版本号”

```
devzkndeMacBook-Pro:iOS-private-api-checker devzkn$ ls -lrt /usr/bin/python*
-rwxr-xr-x  4 root  wheel    925 Jul 16  2017 /usr/bin/python-config
-rwxr-xr-x  1 root  wheel  66880 Oct 26  2017 /usr/bin/python
-rwxr-xr-x  1 root  wheel  66880 Oct 26  2017 /usr/bin/pythonw
lrwxr-xr-x  1 root  wheel     75 Nov 24  2017 /usr/bin/python2.7 -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7
lrwxr-xr-x  1 root  wheel     82 Nov 24  2017 /usr/bin/python2.7-config -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/python2.7-config
lrwxr-xr-x  1 root  wheel     76 Nov 24  2017 /usr/bin/pythonw2.7 -> ../../System/Library/Frameworks/Python.framework/Versions/2.7/bin/pythonw2.7
```


```
devzkndeMacBook-Pro:iOS-private-api-checker devzkn$ ls -lrt /usr/local/bin/python*
lrwxr-xr-x  1 devzkn  wheel  38 Apr  4 14:59 /usr/local/bin/python-build -> ../Cellar/pyenv/1.2.3/bin/python-build
lrwxr-xr-x  1 devzkn  wheel  34 Apr  4 16:42 /usr/local/bin/python3 -> ../Cellar/python/3.6.5/bin/python3
lrwxr-xr-x  1 devzkn  wheel  41 Apr  4 16:42 /usr/local/bin/python3-config -> ../Cellar/python/3.6.5/bin/python3-config
lrwxr-xr-x  1 devzkn  wheel  36 Apr  4 16:42 /usr/local/bin/python3.6 -> ../Cellar/python/3.6.5/bin/python3.6
lrwxr-xr-x  1 devzkn  wheel  43 Apr  4 16:42 /usr/local/bin/python3.6-config -> ../Cellar/python/3.6.5/bin/python3.6-config
lrwxr-xr-x  1 devzkn  wheel  37 Apr  4 16:42 /usr/local/bin/python3.6m -> ../Cellar/python/3.6.5/bin/python3.6m
lrwxr-xr-x  1 devzkn  wheel  44 Apr  4 16:42 /usr/local/bin/python3.6m-config -> ../Cellar/python/3.6.5/bin/python3.6m-config
```

```
devzkndeMacBook-Pro:iOS-private-api-checker devzkn$ which python
/Users/devzkn/.pyenv/shims/python
```

#### `Python`是一个高层次的结合了解释性、编译性、互动性和面向对象的脚本语言。

>* Python 是一种解释型语言： 开发过程中没有了编译这个环节。类似于PHP和Perl语言。

>* Python 是交互式语言： 这意味着，您可以在一个Python提示符，直接互动执行写你的程序。

>* Python 是面向对象语言: 这意味着Python支持面向对象的风格或代码封装在对象的编程技术。
>* 可扩展性：相比 shell 脚本，Python 提供了一个更好的结构，且支持大型程序。

# 运行Python 


#### 1、交互式解释器

```
>>> 2+2
4
```


#### 2、命令行脚本

```
/usr/bin/python iOS_private.py
```


#### 3、集成开发环境（IDE：Integrated Development Environment）

>* `IDLE` 

>* [`pycharm `](https://www.jetbrains.com/pycharm/)

```
/usr/local/bin/charm

1)press ⌘O (Navigate | Class) 
2)using ⇧⌘O (Navigate | File)
3)pressing ⌥F7 (Find Usages in the popup menu)
4)press F1 (View | Quick Documentation).
5) ⇧F6 (Refactor | Rename)
6)To quickly select the currently edited element (class, file, method or field) in any view (Project view, Structure view or other), press ⌥F1.
7)⌘E (View | Recent Files)
8)⌃O (Code | Override Methods).
```


# Python标识符




# [See Also ]( https://www.python.org/)

>* [class-dump-z source](https://code.google.com/archive/p/networkpx/source/default/source)

>* [setup](https://devguide.python.org/setup/#get-the-source-code)
>
>* [Python Developer’s Guide¶](https://devguide.python.org/#python-developer-s-guide)
>
>* [PythonEditors](https://wiki.python.org/moin/PythonEditors)
>
>* [ IDEs ](https://wiki.python.org/moin/IntegratedDevelopmentEnvironments)
>
>* [ BeginnersGuide/Download wiki page](https://wiki.python.org/moin/BeginnersGuide/Download)

>* [Start with our Beginner’s Guide](https://www.python.org/about/gettingstarted/)
>* [docs.python.org](https://docs.python.org/3/)
>* [doc](https://www.python.org/doc/)

>* [tutorial](https://docs.python.org/3.7/tutorial/)

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost Python_brief_introduction Python 简介 -t py
> #原来""的参数，需要自己加上""
```

