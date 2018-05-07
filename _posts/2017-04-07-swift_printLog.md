---
layout:     post
title:      swift_printLog
subtitle:   利用`file`、`line`、`column `、`function `强化swift中的print
date:       2017-04-07
author:     
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - iOS
    - Swift
    - Xcode
    - Debug
---


# 前言
>* 在 Swift 中，编译器为我们准备了几个很有用的编译符号，它们分别是：

<table><thead>
<tr>
<th>符号</th>
<th>类型</th>
<th>描述</th>
</tr>
</thead><tbody>
<tr>
<td>#file</td>
<td>String</td>
<td>包含这个符号的文件的路径</td>
</tr>
<tr>
<td>#line</td>
<td>Int</td>
<td>符号出现处的行号</td>
</tr>
<tr>
<td>#column</td>
<td>Int</td>
<td>符号出现处的列</td>
</tr>
<tr>
<td>#function</td>
<td>String</td>
<td>包含这个符号的方法名字</td>
</tr>
</tbody></table>


# 利用编译符号强化print 

>* 有了上面的这些编译符号，我们就可以自定义一个输出函数：`printm`
><script src="https://gist.github.com/zhangkn/9cb1b6fa6485f6df0cdd3441b103746b.js"></script>
>


# see also

- [《LOG 输出》](http://swifter.tips/log/) - 王巍 (@ONEVCAT)