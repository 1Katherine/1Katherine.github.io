---
title: hexo常用命令
date: 2022-06-22 11:02:00
tags: hexo
categories: 技巧小知识
---



## 前言

方便查阅，将本人常用指令汇总

## 常用指令

由于设置了github多设备同步博客，因此在使用博客之前需要进行拉取同步

```
git pull
```

新建文章

```
hexo n "文章名"
```

文章上传服务器

```
# 清除缓存&&生成静态网页&&将本地数据部署到服务器
hexo clean && hexo g && hexo d
```