---
layout: post
title: two_day_UIImageView_UILabel_Plist
date: 2018-06-04
tag: objc
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 02天-图片浏览
---

# UILabel的基本设置

要想让UILabel自动换行，设置Lines为0即可

# 什么是Plist文件

>* 直接将数据直接写在代码里面，不是一种合理的做法。如果数据经常改，就要经常翻开对应的代码进行修改，造成代码扩展性低

>* 解析Plist文件

<script src="https://gist.github.com/zhangkn/9afc150da979bac57a8aa76861f82f3c.js"></script>


# UIImageView帧动画

>* 相关属性和方法

<script src="https://gist.github.com/zhangkn/e996a4f94c18152e8dd4aab2f5ad8b13.js"></script>

# UIImage的2种加载方式

<script src="https://gist.github.com/zhangkn/8b5b8342808f6a7ca620a5123c5d187c.js"></script>


# 重复代码的封装抽取

>* 抽取代码的思路

```
1)将相同的代码放到一个方法中
2)将不同的值当做方法参数传进来
```

# 补充

>* JPG: 压缩比比较高，通常用于照片、网页
	属于有损压缩（噪点）
	解压缩时，对CPU消耗大，意味慢，费电
>* PNG：压缩比较高
	无损压缩
	解压缩效率高，对CPU消耗小，苹果推荐使用的

# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost two_day_UIImageView_UILabel_Plist 02天-图片浏览 -t objc
> #原来""的参数，需要自己加上""
```
