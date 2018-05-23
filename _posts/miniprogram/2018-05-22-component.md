---
layout: post
title: component
date: 2018-05-22
tag: miniprogram
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: 组件是视图层的基本组成单元,通过组合这些基础组件进行快速开发
---


# component

```xml
<tagname property="value">
  Content goes here ...
</tagname>
```



# 新增 remove-console
>* package.json
>
```
+    "babel-plugin-transform-remove-console": "^6.9.1",
```

>*  wepy.config.js
>
```js
module.exports.compilers.babel.plugins.push('transform-remove-console')
```

# 字符串操作

>* 去除第一个字符
>```
>    let hashData = BW.createHash(BW.reqstr.Gamecontinue.substring(1))
>```


# See Also 

```
+       "libVersion": "2.0.6", 这个库才可以获取群ID
```

>* [es6](http://es6.ruanyifeng.com/#docs/intro)
>

>* font-family
>
```css
font-family: "Helvetica Neue", Helvetica, Arial, "PingFang SC", "Hiragino Sans GB", "Heiti SC", "Microsoft YaHei", "WenQuanYi Micro Hei", sans-serif;
```

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost component 组件是视图层的基本组成单元,通过组合这些基础组件进行快速开发 -t miniprogram
> #原来""的参数，需要自己加上""
```

