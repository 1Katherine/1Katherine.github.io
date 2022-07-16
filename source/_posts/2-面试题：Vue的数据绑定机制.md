---
title: 2-面试题：Vue的数据绑定机制
date: 2022-07-14 16:00:17
tags: 双向数据绑定
categories: 前端面试题
asd

---



# Vue的数据绑定机制

## 参考资料

1. https://febook.hzfe.org/awesome-interview/book1/frame-vue-data-binding



## 相关问题与回答

### 1. Vue是如何实现双向绑定的？



Vue 通过 `v-model` 实现双向绑定。

v-model其实就是v-bind绑定value和v-on监听input事件的结合体。

~~~javascript
v-model = v-bind:value + v-on:input
~~~

用v-bind:value + v-on:input来模拟实现v-model

~~~html
<input type="text" :value="username" @input="username = $event.target.value" />
~~~

例子解释：

1. 通过 `v-bind:value` 绑定 username 变量，每次输入内容的时候触发`input事件`
2. 通过事件对象参数 event.target.value 获得输入的内容，并且把这个内容赋值给username
3. 此时更改username时input输入框会变化，更改input输入框时username变量会变，从而实现了v-model的双向绑定功能





### 2. vue是如何实现数据劫持的？