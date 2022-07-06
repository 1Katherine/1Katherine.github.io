---
title: 项目公用框架组件-tablelist
date: 2022-07-06 23:28:41
tags: 前端、项目
categories: 实验室项目
---



[TOC]



### 公关组件（无需修改）

#### table.vue

通过props实现父传子数据显示，$emit实现子传父操作

~~~vue
<template>
  <div>
    <el-card>
      <el-table ref="table" v-loading="loading" :data="dataList" style="width: 100%" stripe border>
        <!-- v-for 动态渲染表格 template -->
        <template v-for="(item,index) in keys">
          <el-table-column :key="index" :prop="item.value" :label="item.label" :width="item.width" />
        </template>
        <el-table-column label="操作">
          <!-- 通过作用域插槽，通过点击行，获得父组件行的数据 -->
          <template slot-scope="{ row }">
            <el-button
              size="small"
              type="primary"
              @click="handleClick(row)"
            >编辑</el-button>
            <el-button
              size="small"
              type="danger"
              @click="handleDelete(row)"
            >删除</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>
    <!-- 分页组件 -->
    <el-row justify="center" type="flex" align="middle" style="height: 60px">
      <el-pagination
        :current-page="page.currentPage"
        :page-size="page.pageSize"
        layout="prev, pager, next"
        :total="page.total"
        @current-change="handleCurrentChange"
      />
    </el-row>
  </div>
</template>

<script>
export default {
  props: {
    // 表格显示信息
    dataList: {
      type: Array,
      default: null
    },
    keys: {
      type: Array,
      default: null
    },
    // 分页数据
    page: {
      type: Object,
      default: null
    },
    loading: {
      type: Boolean,
      default: false
    }

  },
  data() {
    return {

    }
  },
  created() {
    console.log('loading', this.loading)
  },
  methods: {
    handleClick(row) {
      this.$emit('handleClick', row)
    },
    handleDelete(row) {
      this.$emit('handleDelete', row)
    },
    handleCurrentChange(newPage) {
      this.$emit('handleCurrentChange', newPage)
    }
  }

}
</script>

<style>

</style>

~~~



#### dialog.vue 

通过具名插槽插入表单输入框、选择框、等等

~~~vue
<template>
  <!-- 新增/编辑对话框 -->
  <el-dialog :title="titleName" :visible="showDialog" :before-close="btnCancel" style="margin-top: 120px;">
    <el-form
      ref="form"
      style="overflow-y: auto; height: 485px; margin-top: 10px"
      label-width="150px"
      :rules="rules"
      :model="form"
    >
      <slot name="dialog" />
    </el-form>
    <!-- 弹框底部 -->
    <template v-slot:footer>
      <el-row class="footer" type="flex" justify="center">
        <el-col :span="24">
          <el-button @click="btnCancel">取消</el-button>
          <el-button type="primary" @click="onSave()">保存</el-button>
        </el-col>
      </el-row>
    </template>
  </el-dialog>
</template>

<script>
export default {
  props: {
    titleName: {
      type: String,
      default: ''
    },
    showDialog: {
      type: Boolean,
      default: false
    },
    form: {
      type: Object,
      default: null
    },
    rules: {
      type: Object,
      default: null
    }
  },
  data() {
    return {

    }
  },
  methods: {
    // 取消对话框
    btnCancel() {
      // 关闭对话框
      this.$emit('btnCancel', false)
    },
    // 新增or修改
    onSave() {
      this.$refs.form.validate((valid) => {
        // 成功valid为true，失败为false
        if (valid) {
          // 校验通过才会进入下一步
          this.$emit('onSave')
        } else {
          this.$message.error('表单校验不通过')
        }
      })
    }
  }

}
</script>

<style>

</style>

~~~



#### 基础框架 index.vue 引用公用组件

~~~vue
<template>

  <div class="dashboard-container">
    <div class="app-container">
      <h3>软科排名</h3>
      <!-- table组件展示数据 -->
      <Table
        :data-list="dataList"
        :keys="keys"
        :page="page"
        :loading="loading"
        @handleClick="handleClick"
        @handleDelete="handleDelete"
        @handleCurrentChange="handleCurrentChange"
      />

      <!-- 对话框组件 -->
      <add-dialog
        ref="dialog"
        :form="form"
        :rules="rules"
        :show-dialog="showDialog"
        :title-name="titleName"
        @btnCancel="btnCancel"
        @onSave="onSave"
      >
        <!-- 具名插槽传特定的内容 -->
        <template #dialog>
          <el-form-item label="排名编号" prop="qs_code">
            <el-input v-model="form.qs_code" class="input_box" :disabled="titleName.indexOf('新增')!==-1 ? false : true" />
          </el-form-item>
          <el-form-item label="排名" prop="qs_rank">
            <el-input v-model="form.qs_rank" class="input_box" :disabled="titleName.indexOf('新增')!==-1 ? false : true" />
          </el-form-item>
          <el-form-item label="学校编号" prop="qs_school">
            <el-input v-model="form.qs_school" class="input_box" />
          </el-form-item>
          <el-form-item label="学校名称" prop="qs_schoolName">
            <el-input v-model="form.qs_schoolName" class="input_box" />
          </el-form-item>
        </template>
      </add-dialog>

    </div>
  </div>
</template>

<script>
import Table from '@/components/common/table.vue'
import AddDialog from '@/components/common/add-dialog.vue'
export default {
  components: {
    Table,
    AddDialog
  },

  data() {
    return {
      // 表格
      loading: false,
      dataList: [],
      keys: [], // 数据的keys

      // 分页
      page: {
        currentPage: 1,
        pageSize: 10,
        total: 0
      },
      //  对话框
      titleName: '',
      showDialog: false,
      form: {},
      rules: {

      }
    }
  },
  created() {

  },
  methods: {
    // 分页查询所有信息
    getSubjectsByPage() {
      try {
        this.loading = true

        this.keys = [
          { label: '一级学科编号', value: 'subject_code', width: '150px' },
          { label: '创建日期', value: 'create_time', width: '300px' },
          { label: '更新日期', value: 'update_time', width: '300px' },
          { label: '一级学科id', value: 'subject_id', width: '150px' },
          { label: '一级学科名称', value: 'subject_name', width: '300px' },
          { label: '所属学科门类', value: 'discipline_name', width: '150px' }]

        this.loading = false
      } catch (err) {
        console.log(err)
      }
    },

    // 换页操作
    handleCurrentChange(newPage) {
      this.page.currentPage = newPage
      //   重新获取数据
    },
    // 打开对话框
    async handleClick(row) {
      if (row.subject_code) {
        // 编辑
        this.titleName = '编辑一级学科信息'
        this.form = row
      } else {
        // 新增
        this.titleName = '新增一级学科信息'
      }
      this.showDialog = true
    },
    // 关闭对话框
    btnCancel(value) {
      this.showDialog = value
      // 清空子组件表单校验
      this.$refs.dialog.$refs.form.resetFields()
      // 清空数据
      this.form = { 'subject_id': '', 'subject_code': '', 'subject_name': '', 'subject_discipline': '' }
    },
    onSave() {

    },
    // async onSave() {
    //   try {
    //     if (this.titleName.indexOf('新增') !== -1) {
    //       const { data } = await addSubject(this.form)
    //       if (data.code === 200) {
    //         this.$message.success('保存成功！')
    //       } else {
    //         this.$message.error(data.message)
    //       }
    //     } else {
    //       this.form.subject_code = Number.parseInt(this.form.subject_code)
    //       const { data } = await editSubject(this.form)
    //       if (data.code === 200) {
    //         this.$message.success('保存成功！')
    //       } else {
    //         this.$message.error(data.message)
    //       }
    //     }
    //   } catch (err) {
    //     console.log(err)
    //   }

    //   // 刷新数据
    //   this.getSubjectsByPage()
    //   // 关闭对话框
    //   this.showDialog = false
    //   // 清空子组件表单校验
    //   this.$refs.dialog.$refs.form.resetFields()
    //   // 清空数据
    //   this.form = { 'subject_id': '', 'subject_code': '', 'subject_name': '', 'subject_discipline': '' }
    // },

    handleDelete(row) {

    }
    //     async handleDelete(row) {
    //       this.$prompt('请输入DELETE', '确认删除本行数据？', {
    //         confirmButtonText: '确定',
    //         cancelButtonText: '取消',
    //         inputValidator(value) {
    //           return value === 'DELETE'
    //         },
    //         inputErrorMessage: '输入格式不正确，无法删除数据！'
    //       }).then(async() => {
    //         await delSubject(row.subject_code)
    //         this.$message({
    //           type: 'success',
    //           message: '删除数据成功'
    //         })
    //         this.getSubjectsByPage() // 刷新数据
    //       }).catch(() => {
    //         this.$message({
    //           type: 'info',
    //           message: '取消删除操作'
    //         })
    //       })
    //     }
  }

}
</script>

<style lang="scss" scoped>
::v-deep .el-dialog {
  height: 360px;
  .el-dialog__body {
    height: 260px;
    overflow: hidden;
    padding: 5px 20px 0px 20px;
  }
}
</style>

~~~

