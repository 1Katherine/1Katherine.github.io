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





~~~
school_name
school_major_code
province_code
zs_school_major_year
zslx_code
pc_code
zs_school_major_qj_flag
zs_school_major_number
~~~

~~~
     //定义校验规则
            let validateFileUrl = (rule, value, callback) => {
                if(['正高级职称','高级职称','中级职称'].includes(this.form.jobTitle)){  
                  if(this.jobTitleList.length == 0  && !this.form.jobCertificate){
                      this.$message.warning('请上传职称证书！')
                      callback(new Error("请上传文件"))
                  }else{
                   return callback()
                }
                }
            }
             let validateFile = (rule, value, callback) => {
               if(this.form.practice !== '其他'){
                  if(this.practiceList.length == 0  && !this.form.practiceCertificate){
                      this.$message.warning('请上传执业资格证书！')
                      callback(new Error("请上传文件"))
                  }else{
                   return callback()
                }
               }
            }
~~~



技经项目

~~~
姓名 name
性别 gender
身份证号 idCard
参加工作时间 hiredate
出生年月 birthday
政治面貌politicalStatus

籍贯 nativePlace
初始学历毕业院校 undergraduateSchool
初始学历 undergraduate
初始学历学校类型 undergraduateSchoolType
初始学历专业 undergraduateMajor
初始学历毕业时间 undergraduateDate
最高学历 graduate
最高学历毕业院校 graduateSchool
最高学历专业 graduateMajor
最高学历毕业时间 graduateDate
最高学历学校类型 graduateSchoolType

职务(湖北公司职级) position
工作岗位 jobType
工作年限 workYears
所在单位 workUnit
职称 jobTitle
职称证书 jobCertificate
执业资格 practice
执业资格证书 practiceCertificate
外语-计算机水平 languageSkill
~~~

~~~
timeInlandSelect
timeInlandInput
timeInlandInputRef
timeInlandSpanRef

timeAbroadSelect
timeAbroadInput
timeAbroadInputRef
timeAbroadSpanRef

项目前期营销
{
  "code": 200,
  "message": "success",
  "data": {
    "oversea": [
      "驻外"
    ]
  }
}
项目管理本部
{
  "code": 400,
  "message": "查询结果为空",
  "data": null
}
项目管理驻外
{
  "code": 200,
  "message": "success",
  "data": {
    "oversea": [
      "驻外一年内",
      "驻外1至3年",
      "驻外三年以上"
    ]
  }
}
~~~

