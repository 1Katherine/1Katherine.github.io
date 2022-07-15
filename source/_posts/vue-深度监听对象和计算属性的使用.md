---
title: vue - 深度监听对象和计算属性的使用
date: 2022-07-13 11:58:35
tags:前端 vue
categories: 前端

---

a深度‘

## 计算属性







## 监听







## 监听计算属性

















备份

~~~vue
  watch: {
    // newValue 新数据（本次父组件传递的数据）
    // oldValue 旧数据（上一次父组件传递的数据）
    allprovinces(newValue, oldValue) {
      if (newValue) {
        this.getCitiesByPage()
        this.allprovinces_total = newValue
        // this.loading = false // 数据获取完毕，关闭表单加载
      }
    }
  },
~~~

