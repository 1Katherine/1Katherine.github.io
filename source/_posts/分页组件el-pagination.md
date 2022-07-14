---
title: 分页组件el-pagination
date: 2022-06-24 15:59:36
tags: 分页组件
categories: element-ui
---



[TOC]

## 前言

本篇文章用于简要描述el-table和el-pagination之间如何交互，以及编码中遇到的问题。

数据：静态假数据（用于学习和测试，自己编的数据，请勿当真）

### el-pagination使用方式

> 官方地址：https://element.eleme.cn/#/zh-CN/component/pagination#dai-you-bei-jing-se-de-fen-ye

#### 基础用法

![image-20220624160229958](分页组件el-pagination/image-20220624160229958.png)

~~~vue
<el-pagination
               :current-page="page.page"
               :page-size="page.size"
               layout="prev, pager, next"
               :total="page.total"
               @current-change="handleCurrentChange"
               />
~~~

数据

~~~javascript
  data() {
    return {
      // 接收表格数据（在mounted中，获取数据，同时计算数据总数）
      datalist: [],
      // 分页相关数据
      page: {
        page: 1, // 当前页数
        size: 10, // 每一页放的数据个数
        total: 0 // 数据总数，需要在所有数据挂载完毕后才能在mounted中计算数据总数，
      },
    }
  },
~~~

注释：

（1）**datalist**：表格展示数据。由于需要分页组件需要计算总数据量，所以需要把所有页面数据都加载完之后，才能计算数据长度。故选择created()或mounted()生命周期函数。此处使用mounted()即刷新渲染html页面会重新运行mounted函数。

**created**(): 在所有数据和函数加载完成后，未渲染html之后就执行

**mounted**(): 在页面刷新后，即渲染完html之后才会重新执行

**踩坑！**声明周期函数不写在methods中，写在methods外，和methods平级。很容易在编码时自以为函数都要写在methods里面，导致生命周期函数不执行。

（2）page对象：存储与分页相关的数据

**page**：用于当前页面的显示。当page值发生改变时，赋值给表格的显示数据也要改变。（分页组件通过这个与表格组件交互）

注：分页组件和表格组件是两个独立组件，需要通过函数将两者连接起来，实现不同页面显示不同内容。如果是动态数据，就把page对象传递给后端接口，由接口根据page对象返回不同的数据给表格显示。

**total**：通过设置了total实现分页组件下面的 1 2 3 4...10 数字显示。初始为0，通过mounted函数中计算总数居量

```javascript
  mounted() {
    this.page.total = tableList.length // 修改分页组件的总数居数量
    this.datalist = tableList.slice(0, this.page.size) // 为表格附上第一页的数据
  },
```

注：测试发现，此处使用created也能实现效果。

最终解决方法：通过getDataList()函数获取数据，将数据赋值给datalist，在created中调用datalist。

~~~javascript
    getSchoolList() { // 根据页码获取当前所有数据
      this.page.total = tableList.length // 修改分页组件的总数居数量
      this.datalist = tableList.slice((this.page.page - 1) * this.page.size,
        this.page.page * this.page.size) // 为表格附上该页面的数据
    },
~~~

~~~javascript
  created() {
    this.getSchoolList() // 获取所有数据
  },
~~~



#### 函数

**@current-change="handleCurrentChange"** 

用于设置改变当前页面之后显示的不同数据。通过这个方法实现分页组件与表格数据的交互。

```javascript
handleCurrentChange(newPage) {
    this.page.page = newPage // 修改当前页
    this.getPageList() // 获取当前页的数据给datalist
},
// 获取当前页的数据
getPageList() {
        // 取出newPage页的数据给表格数据显示
        this.datalist = tableList.slice(
            (this.page.page - 1) * this.page.size,
        this.page.page * this.page.size
        )
    }
// 将获取数据单独放在一个函数中，方便编辑数据、新增数据、删除数据后重新调用获取数据的函数，重新渲染页面数据。
```

#### **踩坑2！**

获取当前页数据时，我一开始使用splice，导致每次数组时都改变了原数组。

##### 截取数组元素方法

splice() : 会改变原数组

slice(): 不会改变原数组

## 总代码

### html

```html
<template>
  <div class="dashboard-container">
    <div class="app-container">
      <!-- 信息显示表格 -->
      <el-table :data="datalist" style="width: 100%" stripe border>
        <el-table-column fixed="" type="index" label="序号" width="100" />
        <el-table-column prop="school_id" label="学校编号" width="140" />
        <el-table-column label="学校名" width="140">
          <template slot-scope="scope">
            <el-tag size="medium">{{ scope.row.school_name }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column prop="school_level" label="等级" width="100" />
        <el-table-column prop="school_type" label="类型" width="140" />
        <el-table-column prop="school_nature" label="办学性质" width="140" />
        <el-table-column prop="school_region" label="区域" width="100" />
        <el-table-column prop="school_province" label="省份" width="100" />
        <el-table-column prop="school_city" label="城市" width="100" />
        <el-table-column prop="school_belong" label="隶属单位" width="100" />
        <el-table-column prop="school_rk" label="最新软科排名" width="80" />
        <el-table-column prop="school_wsl" label="最新校友会排名" width="80" />
        <el-table-column prop="school_xyh" label="最新武书连排名" width="80" />
        <el-table-column prop="school_qs" label="最新QS排名" width="80" />
        <el-table-column prop="school_us" label="最新US排名" width="80" />
        <el-table-column prop="school_tws" label="最新泰晤士排名" width="80" />
        <el-table-column
          prop="school_requirments"
          label="报考要求"
          width="140"
        />
        <el-table-column width="200px" label="操作">
          <!-- 通过作用域插槽，通过点击行，获得父组件行的数据 -->
          <template slot-scope="scope">
            <el-button
              size="small"
              type="primary"
              @click="handleClick(scope.row)"
            >编辑</el-button>
            <el-button
              size="small"
              type="danger"
              @click="handleDelete(scope.row)"
            >删除</el-button>
          </template>
        </el-table-column>
      </el-table>
      <!-- 分页组件 -->
      <el-row justify="center" type="flex" align="middle" style="height: 60px">
        <el-pagination
          :current-page="page.page"
          :page-size="page.size"
          layout="prev, pager, next"
          :total="page.total"
          @current-change="handleCurrentChange"
        />
      </el-row
    </div>
  </div>
</template>
```

### javascript

```javascript
<script>
var data = {
  school_id: '10074',
  school_name: '华中科技大学',
  school_level: 'B',
  school_type: '本科一批',
  school_nature: '本科',
  school_region: '中部',
  school_province: '湖北',
  school_city: '武汉',
  school_211: 1,
  school_985: 1,
  school_belong: '湖北省',
  school_rk: 8,
  school_xyh: 12,
  school_wsl: 22,
  school_qs: 69,
  school_us: 178,
  school_tws: 7,
  school_requirments: '报考要求'
}
var data1 = {
  school_id: '20046',
  school_name: '武汉大学',
  school_level: 'B',
  school_type: '本科一批',
  school_nature: '本科',
  school_region: '中部',
  school_province: '湖北',
  school_city: '武汉',
  school_211: 1,
  school_985: 1,
  school_belong: '湖北省',
  school_rk: 9,
  school_xyh: 10,
  school_wsl: 80,
  school_qs: 99,
  school_us: 237,
  school_tws: 6,
  school_requirments: '报考要求'
}
var tableList = [
  data,
  data1,
  data,
  data1,
  data,
  data1,
  data,
  data1,
  data,
  data1,
  data,
  data1,
  data,
  data1,
  data,
  data1,
  data1,
  data,
  data1,
  data,
  data1
]
export default {
  name: 'SchoolInfo',
  data() {
    return {
      // 接收表格数据（在mounted中，获取数据，同时计算数据总数
      datalist: [],
      // 分页相关数据
      page: {
        page: 1, // 当前页数
        size: 10, // 每一页放的数据个数
        total: 1000 // 数据总数，需要在所有数据挂载完毕后才能在mounted中计算数据总数，
      },
    }
  },
  mounted() {
    this.page.total = tableList.length // 修改分页组件的总数居数量
    this.datalist = tableList.slice(0, this.page.size) // 为表格附上第一页的数据
  },
  methods: {
    handleCurrentChange(newPage) {
      this.page.page = newPage // 修改当前页
      this.getPageList() // 获取当前页的数据给datalist
    },
    // 获取当前页的数据
    getPageList() {
      // 取出newPage页的数据给表格数据显示
      this.datalist = tableList.slice(
        (this.page.page - 1) * this.page.size,
        this.page.page * this.page.size
      )
    }
</script>
```



### 分页效果展示

<video src="分页组件el-pagination/高考信息系统 - Google Chrome 2022-06-24 16-26-53.mp4"></video>
