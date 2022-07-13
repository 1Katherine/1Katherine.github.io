---
title: vue - 父组件清除子组件的表单校验无效问题 表单初始值 nextTick()
date: 2022-07-13 11:59:31
tags: 前端 vue
categories: 

---



## 前置知识

父组件 father.vue 里面有一个ref.dialog的子组件对话框

子组件 add-dialog.vue 里面有一个ref=form的表单

父组件调用子组件中的表单对象，使用表单对象的清除校验方法？

~~~javascript
this.$refs.dialog.$refs.form.resetFields()
~~~

### 坑1

ref获取dom对象的前提，对象要已经挂载到页面上，否则获取不到。

解决：在每次使用$refs使用dom对象前，先打印看看这个对象在这一执行步骤时是否存在了。



### 坑2

resetFields方法的作用

1. 清除表单校验规则（红色警告字体），避免下次一打开表单就显示警告
2. 恢复表单数据到初始值（mounted之前的数据为表单初始值）因此，如果在做表单数据回显时，直接将数据赋值给表单对象，那么该数据就会在挂载前赋值上去，导致表单初始值变成当前的数据对象。可以使用$nextTIck(()=>{})，等数据和dom都挂载完成后，在执行数据回显操作

~~~javascript
// 数据回显放入$nextTIck中执行
 // 在表单mounted之后再赋值，避免重置表单失败（重置表单->恢复mounted之前的初始值)
this.$nextTick(() => {
    this.form = {
        pc_code: row.province_control_pc,
        zslx_code: row.province_control_zslx,
        year: row.province_control_year,
        province_code: row.province_control_province,
        province_score: row.province_control_score,
        province_majorScore: row.province_control_majorScore
    }
})
~~~



## nextTick()方法理解

*将回调函数延迟执行。简单理解：当数据更新了，在dom中渲染后，自动执行该函数*



## 经验

表单数据回显都放在nextTick中赋值，避免表单清除校验时无法清除数据。
