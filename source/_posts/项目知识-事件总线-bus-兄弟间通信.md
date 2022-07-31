---
title: 项目知识 - 事件总线 bus 兄弟间通信
date: 2022-07-30 20:51:18
tags:
- 事件总线
- BUS
categories:
- 项目知识
---





## 前言

当前项目使用tagsView实现多页签效果，并且对已打开的所有多页签使用缓存，避免多次发请求。但是页面之间又是相关关联的，页面A更新后，需要通知页面B重新获取数据。A、B之间的关系可能是父子 ，可能是兄弟，也可能是祖先。因此，采用BUS事件总线实现。



## 使用方式

#### 新增util/bus.js 用于导出事件总线

~~~
// 创建事件总线
import Vue from 'vue'
export const EventBus = new Vue()

~~~



#### 发送事件组件

~~~
import { EventBus } from '@/utils/bus'

// 通知学校一级学科页面刷新
EventBus.$emit('refreshSchoolSubjects')

~~~





#### 接收事件组件

~~~
import { EventBus } from '@/utils/bus'

  mounted() {
    EventBus.$on('refreshSchoolSubjects', () => {
      this.getSchoolSubjectsByPageByConditions()
    })
  },
~~~

