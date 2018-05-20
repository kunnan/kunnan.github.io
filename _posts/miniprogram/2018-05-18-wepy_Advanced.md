---
layout: post
title: wepy_Advanced
date: 2018-05-18
tag: miniprogram
site: https://zhangkn.github.io
catalog: true
author: kunnan
subtitle: wepy进阶介绍
---



# wepy.config.js配置文件说明


# .wpy文件说明


![image](http://wx4.sinaimg.cn/large/006tBeITgy1frfhcfcrktj30fb08c3zg.jpg)


# 实例

>* 在 WePY 中，小程序被分为三个实例：小程序实例App、页面实例Page、组件实例Component。其中Page实例继承自Component
><script src="https://gist.github.com/zhangkn/eed586903cacb5997ab01e62f6b565ba.js"></script>
>
>

#### App实例

>* 通过 `this.$parent` 来获取App实例
>```js
>    this.$parent.checkReg(async () => {
>```
>


#### Page页面实例和Component组件实例

>* Page也是组件,除扩展了页面所特有的config配置以及特有的页面生命周期函数之外，其它属性和方法与Component一致
><script src="https://gist.github.com/zhangkn/be250f3911cd81c8532197091a3ef100.js"></script>
>
>* WePY中的组件都是静态组件，是以组件ID作为唯一标识的，每一个ID都对应一个组件实例
>
>

# computed 计算属性

>* 类型: `{ [key: string]: Function }`
>```
>可直接被当作绑定数据来使用。因此类似于data属性，代码中可通过this.计算属性名来引用，模板中也可通过{{ 计算属性名 }}来绑定数据。
>```
>```
>  // 计算属性aPlus，在脚本中可通过this.aPlus来引用，在模板中可通过{{ aPlus }}来插值
  computed = {
      aPlus () {
          return this.a + 1
      }
  }
>```
>

# watcher 监听器



# props 传值

>* props传值在WePY中属于父子组件之间传值的一种机制，包括静态传值与动态传值。
>```js
>props = {
    // 静态传值
    title: String,
    // 父向子单向动态传值
    syncTitle: {
        type: String,
        default: 'null'
    },
    twoWayTitle: {
        type: Number,
        default: 'nothing',
        twoWay: true
    }
};
>```
>
>








# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost wepy_Advanced wepy进阶介 -t miniprogram
> #原来""的参数，需要自己加上""
```

