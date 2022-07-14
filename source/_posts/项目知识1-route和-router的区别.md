---
title: 项目知识1 - $route和$router的区别
date: 2022-07-14 19:01:41
tags: 路由
categories: 项目知识
---







## \$route和\$router的区别

### 参考资料

1. this.$route和this.$router的区别 [https://www.jianshu.com/p/26631cff7649?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation]

### 问题起源

最近在做el-tab实现多页签的功能，需要通过\$route判断当前路由，\$router.push(path) 实现路由跳转，\$router.back() 返回上一个路由。

### 1.1 \$route对象

表示当前激活的路由的状态信息，包含了当前 URL 解析得到的信息，还有 URL 匹配到的 route records（路由记录）。
`$router会被自动注入每个组件中`，可以直接利用它进行一些信息的获取。即this.\$route来调用。

### 1.2 \$route对象出现地方

- 在组件内，直接通过 this.\$route来调用。前提全局注册路由插件

  ~~~javascript
  import Router from 'vue-router';
  
  Vue.use(Router)
  ~~~

  >  可以在项目中使用vue.use()全局注入一个插件，从而不需要在每个组件文件中import插件。
  >
  > 使用了vue.use()注册插件之后就可以在所有的vue文件中使用路由：this.$route

- 在导航守卫的参数内

  ~~~javascript
  router.beforeEach((to, from, next) => {
    // to 和 from 都是 路由信息对象
  })
  watch: {
    $route(to, from) {
       // to 和 from 都是 路由信息对象
    }
  }
  ~~~

  

### 2.1 $router对象

全局的路由实例，是router构造方法的实例。需要在router/index.js 中使用vue-router库进行创建

~~~javascript
import Router from 'vue-router'

const createRouter = () =>
  new Router({
    routes: [
          {
            path: '/login',
            component: () => import('@/views/login/index'),
            hidden: true
          },

          {
            path: '/404',
            component: () => import('@/views/404'),
            hidden: true
          },
    ]
  })

const router = createRouter()

// 导出router对象
export default router



~~~

### 2.2 其他组件中使用router对象需要先引入

~~~javascript
// 在其他组件中使用路由对象 , 自动以 router/index.js 作为入口
import router from '@/router' 
~~~

### 2.3 路由实例方法push

```kotlin
// 字符串
      this.$router.push('home')
// 对象
      this.$router.push({ path: 'home' })
// 命名的路由
      this.$router.push({ name: 'user', params: { userId: 123 }})
// 带查询参数，变成 /register?plan=123
      this.$router.push({ path: 'register', query: { plan: '123' }})
```

`push方法其实和<router-link :to="...">是等同的`。
 *注意：push方法的跳转会向 history 栈添加一个新的记录，当我们点击浏览器的返回按钮时可以看到之前的页面。*

###  2.4  路由实例方法go

```kotlin
// 页面路由跳转 前进或者后退
this.$router.go(-1) // 后退
```

### 2.5 路由实例方法replace

```kotlin
//push方法会向 history 栈添加一个新的记录，而replace方法是替换当前的页面，
不会向 history 栈添加一个新的记录

<router-link to="/05" replace>05</router-link>

// 一般使用replace来做404页面
this.$router.replace('/')
```
