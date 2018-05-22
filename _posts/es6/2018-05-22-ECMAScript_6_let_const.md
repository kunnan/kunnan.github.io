---
layout: post
title: ECMAScript_6_let_const
date: 2018-05-22
tag: es6
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: let 和 const 命令
---



# let 命令 

ES6 新增了`let`命令，用来声明变量。它的用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。

```
{
  let a = 10;
  var b = 1;
}

a // ReferenceError: a is not defined.
b // 1
```



>* `不存在变量提升`
>```
>变量提升:变量可以在声明之前使用
>```

# const 命令 

const声明一个只读的常量。一旦声明，常量的值就不能改变。






# See Also 

>* [](http://es6.ruanyifeng.com/#docs/let)
>

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost ECMAScript_6_let_const let 和 const 命令 -t es6
> #原来""的参数，需要自己加上""
```

