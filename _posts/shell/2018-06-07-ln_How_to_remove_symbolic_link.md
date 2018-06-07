---
layout: post
title: ln_How_to_remove_symbolic_link
date: 2018-06-07
tag: shell
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: How_to_remove_symbolic_link
---

# 前言

```
devzkndeMacBook-Pro:lib devzkn$ sudo  ln -s  /System/Library/Frameworks/IOKit.framework IOKit.framework
```

# Remove a symlink to a directory

```sh
sudo rm -rf IOKit.framework
```
# deletes the content on the target 
```sh
rm -r link/ 
```

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost ln_How_to_remove_symbolic_link How_to_remove_symbolic_link -t shell
> #原来""的参数，需要自己加上""
```

