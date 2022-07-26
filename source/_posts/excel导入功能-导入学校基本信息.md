---
title: excel导入功能 - 导入学校基本信息
date: 2022-07-26 10:09:09
tags:
categories: 项目知识

---



## 1 前言

需求是给一张excel表格，提供学校的基本信息，将其批量导入后端数据库中

表头如下：

![image-20220726101114066](excel导入功能-导入学校基本信息/image-20220726101114066.png)

## 2 封装excel导入组件

### 2.1 安装xlsx包

~~~
$ npm i xlsx@0.16.8
~~~

### 2.2 创建excel组件和样式(components/UploadExcel/index.vue)

![image-20220726101922096](excel导入功能-导入学校基本信息/image-20220726101922096.png)

~~~vue
<template>
  <div class="upload-excel">
    <div class="btn-upload">
      <el-button
        :loading="loading"
        size="mini"
        type="primary"
        @click="handleUpload"
      >
        点击上传
      </el-button>
    </div>

    <input
      ref="excel-upload-input"
      class="excel-upload-input"
      type="file"
      accept=".xlsx, .xls"
      @change="handleClick"
    >
    <div
      class="drop"
      @drop="handleDrop"
      @dragover="handleDragover"
      @dragenter="handleDragover"
    >
      <i class="el-icon-upload" />
      <span>将文件拖到此处</span>
    </div>
  </div>
</template>

<script>
import XLSX from 'xlsx'
export default {
  props: {
    beforeUpload: Function, // eslint-disable-line
    onSuccess: Function // eslint-disable-line
  },
  data() {
    return {
      loading: false,
      excelData: {
        header: null,
        results: null
      }
    }
  },
  methods: {
    generateData({ header, results }) {
      this.excelData.header = header
      this.excelData.results = results
      this.onSuccess && this.onSuccess(this.excelData)
    },
    handleDrop(e) {
      e.stopPropagation()
      e.preventDefault()
      if (this.loading) return
      const files = e.dataTransfer.files
      if (files.length !== 1) {
        this.$message.error('Only support uploading one file!')
        return
      }
      const rawFile = files[0] // only use files[0]
      if (!this.isExcel(rawFile)) {
        this.$message.error(
          'Only supports upload .xlsx, .xls, .csv suffix files'
        )
        return false
      }
      this.upload(rawFile)
      e.stopPropagation()
      e.preventDefault()
    },
    handleDragover(e) {
      e.stopPropagation()
      e.preventDefault()
      e.dataTransfer.dropEffect = 'copy'
    },
    handleUpload() {
      this.$refs['excel-upload-input'].click()
    },
    handleClick(e) {
      const files = e.target.files
      const rawFile = files[0] // only use files[0]
      if (!rawFile) return
      this.upload(rawFile)
    },
    upload(rawFile) {
      this.$refs['excel-upload-input'].value = null // fix can't select the same excel
      if (!this.beforeUpload) {
        this.readerData(rawFile)
        return
      }
      const before = this.beforeUpload(rawFile)
      if (before) {
        this.readerData(rawFile)
      }
    },
    readerData(rawFile) {
      this.loading = true
      return new Promise((resolve, reject) => {
        const reader = new FileReader()
        reader.onload = (e) => {
          const data = e.target.result
          const workbook = XLSX.read(data, { type: 'array' })
          const firstSheetName = workbook.SheetNames[0]
          const worksheet = workbook.Sheets[firstSheetName]
          const header = this.getHeaderRow(worksheet)
          const results = XLSX.utils.sheet_to_json(worksheet)
          this.generateData({ header, results })
          this.loading = false
          resolve()
        }
        reader.readAsArrayBuffer(rawFile)
      })
    },
    getHeaderRow(sheet) {
      const headers = []
      const range = XLSX.utils.decode_range(sheet['!ref'])
      let C
      const R = range.s.r
      /* start in the first row */
      for (C = range.s.c; C <= range.e.c; ++C) {
        /* walk every column in the range */
        const cell = sheet[XLSX.utils.encode_cell({ c: C, r: R })]
        /* find the cell in the first row */
        let hdr = 'UNKNOWN ' + C // <-- replace with your desired default
        if (cell && cell.t) hdr = XLSX.utils.format_cell(cell)
        headers.push(hdr)
      }
      return headers
    },
    isExcel(file) {
      return /\.(xlsx|xls|csv)$/.test(file.name)
    }
  }
}
</script>

<style scoped lang="scss">
.upload-excel {
  display: flex;
  justify-content: center;
  margin-top: 100px;
  .excel-upload-input {
    display: none;
    z-index: -9999;
  }
  .btn-upload,
  .drop {
    border: 1px dashed #bbb;
    width: 350px;
    height: 160px;
    text-align: center;
    line-height: 160px;
  }
  .drop {
    line-height: 80px;
    color: #bbb;
    i {
      font-size: 60px;
      display: block;
    }
  }
}
</style>

~~~

### 2.3 注册全局的导入excel组件 (components/index.js)

~~~js
import PageTools from './PageTools'
import UploadExcel from './UploadExcel'
export default {
  install(Vue) {
    Vue.component('PageTools', PageTools) // 注册工具栏组件
    Vue.component('UploadExcel', UploadExcel) // 注册导入excel组件
  }
}
~~~



## 3 excel导入步骤

### 3.1 建立公共导入的页面路由 （router/index.js）

~~~js
 // excel导入的路由
  {
    path: '/import',
    component: Layout,
    hidden: true, // 在左侧栏中隐藏
    children: [{
      path: '',
      component: () => import('@/views/import'),
      name: 'Import',
      meta: {
        title: '文件导入' // 左侧导航会读取我们的路由里的meta里面的title作为显示菜单名称
      }
    }]
  },
~~~

### 3.2 创建import路由组件  **`src/views/import/index.vue`**

~~~vue
<template>
  <!-- 公共导入组件 --> 
  <upload-excel :on-success="success" />
</template>

~~~

### 3.3 封装批量导入接口 api/schools.js

~~~js
// 批量新增学校信息
export function importSchoolInfo(data) {
  return request({
    url: '/school/addBatch',
    method: 'post',
    data
  })
}
~~~

### 3.4 实现excel导入

使用type类型，区分不同的批量导入请求。

~~~vue
<template>
  <!-- 引入公共Excel导入组件 -->
  <upload-excel :on-success="success" />
</template>
<script>
import { importSchoolInfo } from '@/api/school'
export default {
  name: 'Import',
  data() {
    return {
      // 为了让这个页面可以服务更多的导入功能，我们可以在页面中用参数来判断
      type: this.$route.query.type
    }
  },
  methods: {
    async success({ header, results }) {
      console.log('success type', this.$route.query)
      if (this.type === undefined) {
        this.$message.error('请关闭当前页面，重新进入')
      } else {
        // 如果是导入员工
        if (this.type === 'schoolBasicInfo') {
          const schoolInfoRelations = {
            学校编号: 'school_id',
            学校名: 'school_name',
            学校等级: 'school_level',
            学校类型: 'school_type',
            办学性质: 'school_nature',
            区域: 'school_region',
            省份: 'school_province',
            城市: 'school_city',
            是否211: 'school_211',
            是否985: 'school_985',
            隶属单位: 'school_belong',
            最新软科排名: 'school_rk',
            最新校友会排名: 'school_xyh',
            最新武书连排名: 'school_wsl',
            最新QS排名: 'school_qs',
            最新US排名: 'school_us',
            最新泰晤士排名: 'school_tws',
            报考要求: 'school_requirments'
          }
          const arr = []
          results.forEach((item) => {
            const userInfo = {}
            Object.keys(item).forEach((key) => {
              userInfo[schoolInfoRelations[key]] = item[key]
            })
            arr.push(userInfo)
          })
          const { data } = await importSchoolInfo(arr) // 调用导入接口
          if (data.code === 200) {
            this.$message.success('批量新增学校基本信息成功！')
          }
          // 跳转回到上一个页面
          this.$router.back()
          // 将信息导入页面从openPages中删除，即关闭导入页面的tagsView
          this.$store.commit('tagsView/REMOVE_PAGE', '/import')
        }
      }
    }
  }
}
</script>
<style />

~~~

### 3.5 在学校界面，点击"导入"按钮，实现批量导入。传入type类型

~~~vue
<el-button type="warning" @click="$router.push('/import?type=schoolBasicInfo')"
>导入</el-button>
~~~

