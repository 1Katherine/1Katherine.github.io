---
title: 项目 - PC适配 - vue.config.js
date: 2022-07-30 11:24:11
tags:
- 屏幕适配
- 缩放页面
- 移动端
- PC端
categories:
- 项目知识
---







## 参考资料

1. 【vue2.0项目PC端随屏幕分辨率与窗口大小自适应】https://blog.csdn.net/weixin_47416539/article/details/125032479
2. 【vue+px2rem实现pc端大屏自适应(rem适配)】https://codeantenna.com/a/G4TWS25Sdt



## PC端适配问题

用于解决最小化浏览器窗口时，字体和样式大小不变，导致的页面混乱问题。

解决方式，将px转换为rem

### 实现步骤

#### 在根目录src中新建utils目录下新建rem.js等比适配文件

我使用的最大放大比例为1 ，呵呵，效果不错！需要自己调试，选出一个合适的值

~~~js
// rem等比适配配置文件
// 基准大小
const baseSize = 16
// 设置 rem 函数
function setRem () {
  // 当前页面屏幕分辨率相对于 1920宽的缩放比例，可根据自己需要修改
  const scale = document.documentElement.clientWidth / 1920
  // 设置页面根节点字体大小（“Math.min(scale, 2)” 指最高放大比例为2，可根据实际业务需求调整）
  document.documentElement.style.fontSize = baseSize * Math.min(scale, 1) + 'px'
}
// 初始化
setRem()
// 改变窗口大小时重新设置 rem
window.onresize = function () {
  setRem()
}

~~~

#### 在main.js中引入适配文件

~~~
import './utils/rem.js'
~~~

#### 安装postcss-pxtorem

~~~
npm i postcss-pxtorem@5.1.1 -D
~~~

#### 在vue.config.js内配置

~~~js
module.exports = {
    css: {
            loaderOptions: {
              postcss: {
                plugins: [
                  require('postcss-pxtorem')({
                    rootValue : 16, // 换算的基数
                    selectorBlackList  : [], // 忽略转换正则匹配项 列入一些ui库, ['.el'] 就是忽略elementUI库
                    propList   : ['*'],
                  }),
                ]
              }
            }
        }
}

~~~



## PC端禁止用户缩放页面

用于禁止移动端双指缩放页面，和PC端ctrl+滚轮缩放页面

### 实现步骤

#### 对于移动端

'可以在[index](https://so.csdn.net/so/search?q=index&spm=1001.2101.3001.7020).html中增加如下代码：

~~~
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0, user-scalable=no">
~~~

#### 对于PC端：禁止ctrl+鼠标滚轮缩放

可以在[index](https://so.csdn.net/so/search?q=index&spm=1001.2101.3001.7020).html中增加如下代码：

~~~js
<script>
 
    document.addEventListener('mousewheel', function (e) {
 
      e = e || window.event;
 
      if ((e.wheelDelta && event.ctrlKey) || e.detail) {
 
        event.preventDefault();
 
      }
 
    }, {
 
      capture: false,
 
      passive: false
 
    });
 
    document.addEventListener('keydown', function (event) {
 
      if ((event.ctrlKey === true || event.metaKey === true)
 
        && (event.keyCode === 61 || event.keyCode === 107
 
          || event.keyCode === 173 || event.keyCode === 109
 
          || event.keyCode === 187 || event.keyCode === 189)) {
 
        event.preventDefault();
 
      }
 
    }, false);
 
  </script>
~~~



