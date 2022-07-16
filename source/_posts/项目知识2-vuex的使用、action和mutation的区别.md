---
title: 项目知识2 - vuex的使用、action和mutation的区别
date: 2022-07-14 21:02:08
tags: vuex
categories: 项目查漏补缺 
---

# vuex的使用、action和mutation的区别

## 参考资料

1. action和mutation区别 [https://www.jianshu.com/p/f9213af4bcb1]



## action和mutation的区别

> action中处理业务代码、异步请求。不能操作State
>
> mutation专注于修改State，理论上是修改State的唯一途径。必须同步执行



## 实现



定义action

~~~javascript
actions: {
  //  获取异步的数据 context表示当前的store的实例 可以通过 context.state 获取状态 也可以通过context.commit 来提交mutations， 也可以 context.diapatch调用其他的action
    getAsyncCount (context) {
      setTimeout(function(){
        // 一秒钟之后 要给一个数 去修改state
        context.commit('addCount', 123)
      }, 1000)
    }
 } 
~~~







## 项目应用

由于地理信息（所有区域、省份、城市）在多个页面都需要获取，如果每个页面都通过请求获取，会重复多次请求同一个地址，导致项目加载效率低。因此采用vuex获取所有地理信息，并保存。每次使用的时候先从state中进行获取，如果state中没有信息再调用请求获取。

#### 需求

获取所有区域信息

- 如果state.regions为空，则自行通过get请求获取所有Regions，并使用setRegions 设置 regions的值
- 每次update、delete、insert后，都要通过get请求重新获取所有Regions

获取所有省份信息

- setRegions 设置 regions的值后需要重新获取所有省份信息
- 编辑省份后需要重新获取所有省份信息

获取所有城市信息

- setRegions 设置 regions的值后需要重新获取城市信息
- 编辑城市后需要重新获取



编辑区域信息后，需要重新获取所有区域、省份、城市

编辑省份后，需要重新所有省份、城市

编辑城市后，需要重新获取所有城市

