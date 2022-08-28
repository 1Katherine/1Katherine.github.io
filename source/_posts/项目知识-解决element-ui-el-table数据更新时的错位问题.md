---
title: 项目知识 - 解决element-ui el-table数据更新时的错位问题
date: 2022-08-02 08:49:33
tags:
- elementui
- el-table
- 数据更新doLayout
categories:
- 项目知识
---



## 参考资料

[解决Element-ui的el-table出现的表格错位问题](https://blog.csdn.net/qq_39998026/article/details/124862604)

在使用element-ui中的el-table时，我们经常会用到fiexd属性，而使用了fixed属性之后，就会容易在各种场景出现表格错位的问题。

查阅element-ui官网，发现官网提供了doLayout方法来解决这个问题



总结容易出现错位问题的几种场景及解决办法

1、数据更新后出现的错位问题

1.1 直接在数据赋值后执行doLayout方法

~~~vue
this.data = data;
// 在数据更新后执行
this.$nextTick(() => {
      // myTable是表格的ref属性值
      if (this.$refs.myTable && this.$refs.myTable.doLayout) {
        this.$refs.myTable.doLayout();
      }
})
~~~


1.2在生命周期updated里执行doLayout方法

~~~vue
updated() {
    // myTable是表格的ref属性值
    if (this.$refs.myTable && this.$refs.myTable.doLayout) {
      this.$refs.myTable.doLayout();
    }
}
~~~



注：参考链接中有三种方法解决错位问题。分为是

1. **数据更新后出现的错位问题**
2. **浏览器窗口大小变化时出现的错位问题**
3. **当有多个Tab标签时，切换标签出现的错位问题**

本文中仅参考第一种 [数据更新错位问题] 就解决了刷新时el-table有一瞬间的错位问题
