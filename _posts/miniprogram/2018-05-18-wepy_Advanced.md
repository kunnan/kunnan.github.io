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




# I 、.wpy文件说明


![image](http://wx4.sinaimg.cn/large/006tBeITgy1frfhcfcrktj30fb08c3zg.jpg)


# II、实例

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

# III、computed 计算属性

>* 类型: `{ [key: string]: Function }`
>```
>可直接被当作绑定数据来使用，类似于data属性。
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




# IV、 props 传值

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


# V、 组件通信与交互


>* `wepy.component`基类提供`$broadcast、$emit、$invoke`三个方法用于组件之间的通信和交互
>

#### `$emit`

>* 子组件：事件发起组件的所有祖先组件会依次接收到`$emit`事件

```js
this.$emit('some-event', 1, 2, 3, 4);
        this.$emit('answer-event', 1)// 触发条件：答对了
```

>* 祖先组件: 事件处理函数需要写在组件和页面的`events`对象

```js
    events = {
        'some-event': (p1, p2, p3, $event) => {
               console.log(`${this.$name} receive ${$event.name} from ${$event.source.$name}`);
        }
       'answer-event': knanswer => {
      if (knanswer === 1) {
        // 处理答对的情况  
      } else {
        // 处理失败的情况
        this.$apply()
      }
     }
    };
```

#### `$broadcast`

`$broadcast`事件是由父组件发起，所有子组件都会收到此广播事件

子组件： 
```js
    'helpTips': () => {
    //处理分享之后的View渲染
      }
```
父组件: 分享回调的处理

```
            this.$broadcast('helpTips')
```




#### `$invoke`

`$invoke`是一个页面或组件对另一个组件中的方法的直接调用，通过传入组件路径找到相应的组件，然后再调用其方法。


>* 想在页面Page_Index中调用组件ComA的某个方法：
```
this.$invoke('ComA', 'someMethod', 'someArgs');
如果想在组件ComA中调用组件ComG的某个方法：
```
>* 
>```
this.$invoke('./../ComB/ComG', 'someMethod', 'someArgs');
```


# VI、 WePY数据绑定方式

`在异步函数中更新数据的时候，必须手动调用$apply方法，才会触发脏数据检查流程的运行`





# See Also 

>* [knpost](https://github.com/zhangkn/KNBin/blob/master/knpost) 
>
```
/Users/devzkn/bin/knpost wepy_Advanced wepy进阶介 -t miniprogram
> #原来""的参数，需要自己加上""
```

