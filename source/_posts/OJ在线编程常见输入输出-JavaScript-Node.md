---
title: OJ在线编程常见输入输出 - JavaScript Node
date: 2022-08-22 21:20:46
tags:
- 算法
categories:
- 算法题
---



### 前言

最近秋招面临找工作的事情，基本上都在忙折刷算法题、背八股文。之前学习 Java、Python 居多。但是工作目标还是前端，因此重新捡起来 JavaScript ，开始了漫漫的刷题路...

在网上找了很多输入输出的资料，有关 JavaScript Node 版本的少之又少，内容又参差不齐。因此，决定自己整理一个文档，用于日后的复习，提供刷题参考。



### 总结：

每一行输入都会调用一次 `rl.on('line',...）`，因此，如果每一行输入逻辑一样，则可以统一处理。如果每一行的输入逻辑不一样，比如第一行是测试组数，第二行开始才是测试数据，需要在 on 函数中判断当前是否是第一行，还是测试数据行。



### 多行输入 - 不知道行数

链接：https://ac.nowcoder.com/acm/contest/5652/A
来源：牛客网

#### 输入描述:

```
输入包括两个正整数a,b(1 <= a, b <= 1000),输入数据包括多组。
```

#### 输出描述:

```
输出a+b的结果               
```

#### 输入

```
1 5
10 20
```

#### 输出

```
6
30
```

#### 代码

```javascript
const readline = require('readline')

const rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
})

// 每行执行一次
rl.on('line', function(line) {
    
    let a = Number.parseInt(line.split(' ')[0])
    let b = Number.parseInt(line.split(' ')[1])
    
    console.log(a+b)
})
```

### 多行输入 - 知道行数

链接：https://ac.nowcoder.com/acm/contest/5652/B
来源：牛客网

#### 输入描述:

```
输入第一行包括一个数据组数t(1 <= t <= 100)
接下来每行包括两个正整数a,b(1 <= a, b <= 1000)
```

#### 输出描述:

```
输出a+b的结果           
```

#### 输入

```
2
1 5
10 20
```

#### 输出

```
6
30
```

#### 代码：

```javascript
const readline = require('readline')

const rl = readline.createInterface({
    input: process.stdin,
    output:process.stdout
})

let num = 0 // 记录组数
rl.on('line', function(line) {
    if (num === 0) {
        num = Number.parseInt(line)
        return 
    } 
    let a = Number.parseInt(line.split(' ')[0])
    let b = Number.parseInt(line.split(' ')[1])
    console.log(a+b)
})
```



### 多组输入、结束标准

#### 题目描述                    

  计算a+b 

#### 输入描述:

```
输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据有多组, 如果输入为0 0则结束输入
```

#### 输出描述:

```
输出a+b的结果             
```

#### 输入

```
1 5
10 20
0 0
```

#### 输出

```
6
30
```

#### 代码

```javascript
const readline = require('readline')

const rl = readline.createInterface({
  input: process.stdin,
  output: process.stdout,
})

rl.on('line', function (line) {
  let a = Number.parseInt(line.split(' ')[0])
  let b = Number.parseInt(line.split(' ')[1])

  /**
   *  方法一：关闭输入- 完成本次剩余逻辑后，关闭输入
   */
  if (a === b && a === 0) {
    rl.close()
  } else {
    console.log(a + b)
  }

  /**
   *  方法二: 关闭输入 结合 return ，不用 else 判断了
   */
  if (a === b && a === 0) {
    rl.close()
    return
  }
  console.log(a + b)

  /**
   * 方法三：return 本行 - 不继续剩余逻辑
   */
  if (a === b && a === 0) {
    return
  }
  console.log(a + b)
})

```



### 多组输入、结束标准2

链接：https://ac.nowcoder.com/acm/contest/5652/D
来源：牛客网

#### 输入描述:

```
输入数据包括多组。
每组数据一行,每行的第一个整数为整数的个数n(1 <= n <= 100), n为0的时候结束输入。
接下来n个正整数,即需要求和的每个正整数。
```

#### 输出描述:

```
每组数据输出求和的结果             
```

#### 输入

```
4 1 2 3 4
5 1 2 3 4 5
0
```

#### 输出

```
10
15
```

```javascript
const readline = require('readline')

const rl = readline.createInterface({
    input: process.stdin,
    outpu: process.stdout
})



// 每一行 都会重新调用这个方法
rl.on('line', function(line) {
    const arr = line.split(' ')
    const n =  Number.parseInt(line.split(' ')[0]) // 记录每一行数组元素的个数
        if (n === 0) {
        // 关闭输入 return
        rl.close()
        return 
    }
    let sum = 0
    // 每组数据输出求和的结果
    for (let i = 1; i < arr.length; i++) {
        sum += Number.parseInt(arr[i])
    }
    console.log(sum)
})
```



### 多组输入

链接：https://ac.nowcoder.com/acm/contest/5652/E
来源：牛客网

#### 输入描述:

```
输入的第一行包括一个正整数t(1 <= t <= 100), 表示数据组数。
接下来t行, 每行一组数据。
每行的第一个整数为整数的个数n(1 <= n <= 100)。
接下来n个正整数, 即需要求和的每个正整数。
```

#### 输出描述:

```
每组数据输出求和的结果
```

​                            示例1                        

#### 输入

```
2
4 1 2 3 4
5 1 2 3 4 5
```

#### 输出

```
10
15
```

#### 代码

```javascript
const readline = require('readline')

const rl = readline.createInterface({
    input: process.stdin,
    outpu: process.stdout
})


let num = 0 // 计算 数据的组数
// 每一行 都会重新调用这个方法
rl.on('line', function(line) {
//     第一行逻辑
    if (num === 0) {
        num = Number.parseInt(line) 
        return 
    }
//     其他行逻辑(不加 else 也可以，因为第一行 return 了；如果第一行没有 return，则必须加 else，不然会在第一行时继续往下执行)
    const arr = line.split(' ')
    let sum = 0
    // 每组数据输出求和的结果
    for (let i = 1; i < arr.length; i++) {
        sum += Number.parseInt(arr[i])
    }
    console.log(sum)
})
```



### 字符串排序后输出

链接：https://ac.nowcoder.com/acm/contest/5652/H
来源：牛客网

#### 输入描述:

```
输入有两行，第一行n

第二行是n个字符串，字符串之间用空格隔开
```

#### 输出描述:

```
输出一行排序后的字符串，空格隔开，无结尾空格
```

​                            示例1                        

#### 输入

```
5
c d a bb e
```

#### 输出

```
a bb c d e
```

#### 代码

```javascript
const readline = require('readline')

const rl = readline.createInterface({
    input: process.stdin,
    outpu: process.stdout
})

/**
* 排序字符串数组
*/
let sort = (arr) => {
    return arr.sort().join(' ')
}

let num = 0
// 每一行 都会重新调用这个方法
rl.on('line', function(line) {
    if (num === 0) {
        num = Number.parseInt(line)
        return 
    }
    const arr = line.split(' ')
    const res = sort(arr)
    console.log(res)
})
```

