---
title: vuex基础 - store、state、mutations、actions、局部作用域
date: 2022-07-27 10:23:52
tags: 
- vuex
- store
categories: 
- 项目知识

---

## 结论

1. 修改state状态必须通过**`mutations`**
2. **`mutations`**只能执行同步代码，类似ajax，定时器之类的代码不能在mutations中执行
3. 执行异步代码，要通过**`actions`**，然后将数据提交给mutations才可以完成数据更新
4. state的状态即共享数据可以在组件中引用
5. 组件中可以调用 state 、mutations、 actions



## vuex初始化

1. 创建vue demo

   ~~~
   vue create  demo
   ~~~

2. 安装到**`运行时依赖`**

   ~~~
   npm i vuex --save
   ~~~

3. 在main.js中注册vuex

   ~~~js
   import Vue from 'vue'
   import Vuex from 'vuex'
   Vue.use(vuex)
   const store = new Vuex.Store({})
   new Vue({
     el: '#app',
     store
   })
   ~~~

## state

### **定义state**

~~~js
// 初始化vuex对象
const store = new Vuex.Store({
  state: {
    // 管理数据
    count: 0
  }
})
~~~

### 在组件中获取state

#### 插值表达式 $store.state

~~~vue
<div> state的数据：{{ $store.state.count }}</div>
~~~

#### 计算属性

~~~js
// 把state中数据，定义在组件内的计算属性中
  computed: {
    count () {
      return this.$store.state.count
    }
  }

 <div> state的数据：{{ count }}</div>
~~~

#### 辅助函数 - mapState

第一步：导入mapState

~~~vue
import { mapState } from 'vuex'
~~~

第二步：采用数组形式引入state属性

```js
mapState(['count']) 
```

第三步：利用**延展运算符**将导出的状态映射给计算属性

```js
  computed: {
    ...mapState(['count'])
  }
```

```vue
 <div> state的数据：{{ count }}</div>
```

## mutations

#### 定义mutations

~~~js
const store  = new Vuex.Store({
  state: {
    count: 0
  },
  // 定义mutations
  mutations: {
      // 方法里参数 第一个参数是当前store的state属性
    // payload 载荷 运输参数 调用mutaiions的时候 可以传递参数 传递载荷
    addCount (state, payload) {
        state.count += payload
    },
    addCount2 (state) {
        state.count += 2
    }
  }
})
~~~

### 在组件中调用mutations

#### **原始形式**-$store.commit

~~~vue
<template>
  <button @click="addCount">+1</button>
</template>

<script>
export default {
    methods: {
    //   调用方法
      addCount () {
         // 调用store中的mutations 提交给muations
        // commit('muations名称', 2)
        this.$store.commit('addCount', 10)  // 直接调用mutations
    }
  }
}
</script>
~~~

#### **辅助函数** - mapMutations

~~~vue
import  { mapMutations } from 'vuex'
methods: {
    ...mapMutations(['addCount'])
}

<button @click="addCount(100)">+100</button>
~~~

## actions

### 定义actions

~~~js
 actions: {
  //  获取异步的数据 context表示当前的store的实例 可以通过 context.state 获取状态 也可以通过context.commit 来提交mutations， 也可以 context.diapatch调用其他的action
    getAsyncCount (context) {
      setTimeout(function(){
        // 一秒钟之后 要给一个数 去修改state
        context.commit('addCount', 123)
      }, 1000)
    },
    // 带参数
    getAsyncCount2 (context, payload) {
      setTimeout(function(){
        // 一秒钟之后 要给一个数 去修改state
        context.commit('addCount', payload)
      }, 1000)
    }
 } 
~~~

### 在组件中调用actions

#### **原始调用** - $store.dispatch('actionName', params)

~~~js
// 无参数调用
 addAsyncCount () {
     this.$store.dispatch('getAsyncCount')
 }
// 参数调用
 addAsyncCount () {
     this.$store.dispatch('getAsyncCount', 123)
 }
~~~

#### 辅助函数 - mapActions([])

~~~vue
import { mapActions } from 'vuex'
methods: {
    ...mapActions(['getAsyncCount'])
}
// 调用
<button @click="getAsyncCount(111)">+异步</button>
~~~

## getters

### 定义getters

~~~js
  getters: {
    // getters函数的第一个参数是 state
    // 必须要有返回值
     filterList:  state =>  state.list.filter(item => item > 5)
  }
~~~

### 在组件中使用getters

#### 原始方式 - $store.getters

~~~vue
<div>{{ $store.getters.filterList }}</div>
~~~

#### **辅助函数** - mapGetters([])

~~~vue
computed: {
    ...mapGetters(['filterList'])
}

 <div>{{ filterList }}</div>
~~~



## Vuex - 模块化

### 应用

定义两个模块   **user** 和  **setting**

user中管理用户的状态  token 

setting中管理 应用的名称 name

### 定义两个模块

~~~vue
const store  = new Vuex.Store({
  modules: {
    user: {
       state: {
         token: '12345'
       }
    },
    setting: {
      state: {
         name: 'Vuex实例'
      }
    }
  })
~~~

### 使用 $store.**`state`**.**`模块名称`**.**`属性名`**

~~~vue
<template>
  <div>
      <div>用户token {{ $store.state.user.token }}</div>
      <div>网站名称 {{ $store.state.setting.name }}</div>
  </div>
</template>
~~~

使用getters获取属性

~~~js
 getters: {
   token: state => state.user.token,
   name: state => state.setting.name
 } 

 computed: {
       ...mapGetters(['token', 'name'])
 }
~~~

### 模块化中的命名空间

默认情况下，模块内部的 action、mutation 和 getter 是注册在**全局命名空间**的——这样使得多个模块能够对同一 mutation 或 action 作出响应。但是，如果我们想保证内部模块的高封闭性，我们可以采用namespaced来进行设置

~~~js
  user: {
       namespaced: true,
       state: {
         token: '12345'
       },
       mutations: {
        //  这里的state表示的是user的state
         updateToken (state) {
            state.token = 678910
         }
       }
    },
~~~

#### 方案1：**直接调用-带上模块的属性名路径$store.dispatch(modelName/mutationName)**

~~~js
test () {
   this.$store.dispatch('user/updateToken') // 直接调用方法
}
~~~

#### 方案2：**辅助函数-带上模块的属性名路径** -mapMutations([modelName/mutaionName])

~~~vue
  methods: {
       ...mapMutations(['user/updateToken']),
       test () {
           this['user/updateToken']()
       }
   }
  <button @click="test">修改token</button>
~~~

#### 方案3： **createNamespacedHelpers**  创建基于某个命名空间辅助函数

~~~vue
import { mapGetters, createNamespacedHelpers } from 'vuex'
const { mapMutations } = createNamespacedHelpers('user')
<button @click="updateToken">修改token2</button>
~~~

