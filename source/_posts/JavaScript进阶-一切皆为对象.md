---
title: JavaScript进阶 - 面向对象、构造函数
date: 2022-07-31 10:31:01
tags:
- JavaScript
- 构造函数
- 基本数据类型
- 基本引用类型
- 对象
- 数组
- filter
- map
- reduce
categories:
- 前端面试
---



## 面向对象

> 了解面向对象的基础概念，能够利用构造函数创建对象。

### 构造函数

构造函数是专门用于创建对象的函数，如果一个函数使用 `new` 关键字调用，那么这个函数就是构造函数。

```html
<script>
  // 定义函数
  function foo() {
    console.log('通过 new 也能调用函数...');
  }
  // 调用函数
  new foo;
</script>
```

总结：

2. 使用 `new` 关键字调用函数的行为被称为实例化
3. 实例化构造函数时没有参数时可以省略 `()`
4. 构造函数的返回值即为新创建的对象
5. 构造函数内部的 `return` 返回的值无效！

注：实践中为了从视觉上区分构造函数和普通函数，习惯将构造函数的首字母大写。

### 实例成员

通过构造函数创建的对象称为实例对象，实例对象中的属性和方法称为实例成员。

```html
<script>
  // 构造函数
  function Person() {
    // 构造函数内部的 this 就是实例对象

    // 实例对象中动态添加属性
    this.name = '小明';
    // 实例对象动态添加方法
    this.sayHi = function () {
      console.log('大家好~');
    }
  }

  // 实例化，p1 是实例对象
  // p1 实际就是 构造函数内部的 this
  let p1 = new Person();

  console.log(p1);
  console.log(p1.name); // 访问实例属性
  p1.sayHi(); // 调用实例方法
</script>
```

总结：

1. 构造函数内部 `this` 实际上就是实例对象，为其动态添加的属性和方法即为实例成员
2. 为构造函数传入参数，动态创建结构相同但值不同的对象
3. 实例对象的 `constructor`  属性指向了构造函数
4. `instanceof` 用于检测实例对象对应的构造函数

注：构造函数创建的实例对象彼此独立互不影响。

### 静态成员

在 JavaScript 中底层函数本质上也是对象类型，因此允许直接为函数动态添加属性或方法，构造函数的属性和方法被称为静态成员。

```html
<script>
  // 构造函数
  function Person(name, age) {
    // 省略实例成员
  }

  // 静态属性
  Person.eyes = 2;
  Person.arms = 2;
  // 静态方法
  Person.walk = function () {
    console.log('^_^人都会走路...');
    // this 指向 Person
    console.log(this.eyes);
  }
</script>
```

总结：

1. 静态成员指的是添加到构造函数本身的属性和方法
2. 一般公共特征的属性或方法静态成员设置为静态成员
3. 静态成员方法中的 `this` 指向构造函数本身



### 一切皆对象

> 体会 JavaScript 一切皆对象的语言特征，掌握各引用类型和包装类型对象属性和方法的使用。

在 JavaScript 中**最主要**的数据类型有 6 种，分别是字符串、数值、布尔、undefined、null 和 对象，常见的对象类型数据包括数组和普通对象。其中字符串、数值、布尔、undefined、null 也被称为简单类型或基础类型，对象也被称为引用类型。

在 JavaScript 内置了一些构造函数，绝大部的数据处理都是基于这些构造函数实现的，JavaScript 基础阶段学习的 `Date` 就是内置的构造函数。

```html
<script>
  // 实例化
	let date = new Date();
  
  // date 即为实例对象
  console.log(date);
    
    
    简单数据类型：字符串、数字、布尔、undefined、null
    (引用类型)复杂数据类型：对象（数组）
    
</script>
```

甚至字符串、数值、布尔、数组、普通对象也都有专门的构造函数，用于创建对应类型的数据。

#### 引用类型

##### Object

`Object` 是内置的构造函数，用于创建普通对象。

```html
<script>
  // 通过构造函数创建普通对象
  let user = new Object({name: '小明', age: 15});

  // 这种方式声明的变量称为【字面量】
  let student = {name: '杜子腾', age: 21}
  
  // 对象语法简写
  let name = '小红';
  let people = {
    // 相当于 name: name
    name,
    // 相当于 walk: function () {}
    walk () {
      console.log('人都要走路...');
    }
  }

  console.log(student.constructor);
  console.log(user.constructor);
  console.log(student instanceof Object);
</script>
```

下图展示了普通对象在内存中的存储方式：**普通对象数据保存在堆内存之中，栈内存中保存了普通对象在堆内存的地址**。

![memory](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day2\笔记\assets\object-1.png)



**普通对象在赋值时只是复制了栈内中的地址，而非堆内存中的数据**，如下图所示：

![heap-2](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day2\笔记\assets\object-2.png)

普通对象赋值后，`无论修改哪个变量另一个对象的数据值也会相当发生改变`。

总结：

1. 推荐使用字面量方式声明对象，而不是 `Object` 构造函数
2. `Object.assign` 静态方法创建新的对象
3. `Object.keys` 静态方法获取对象中所有属性
4. `Object.values` 表态方法获取对象中所有属性值

面试回答堆与栈的区别：

1. 堆和栈是内存中的数据存储空间
2. 简单类型的数据保存在内存的栈空间中
3. 引用类型的数据保存在内存的堆空间中，栈内存中存取的是引用类型的地址（房间号）

##### Array

`Array` 是内置的构造函数，用于创建数组。

```html
<script>
  // 构造函数创建数组
  let arr = new Array(5, 7, 8);

  // 字面量方式创建数组
  let list = ['html', 'css', 'javascript'];

  console.log(list.constructor);
  console.log(list instanceof Array);
</script>

```

数组在内存中的存储方式与普通对象一样，如下图所示：

![memory](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day2\笔记\assets\array-1.png)

数组在赋值时只是复制了栈内中的地址，而非堆内存中的数据，如下图所示：

![array-2](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day2\笔记\assets\array-2.png)

数组赋值后，无论修改哪个变量另一个对象的数据值也会相当发生改变。

总结：

1. 推荐使用字面量方式声明数组，而不是 `Array` 构造函数
2. 实例方法 `forEach` 用于遍历数组，替代 `for` 循环
3. 实例方法 `filter` 过滤数组单元值，生成新数组
4. 实例方法 `map` 迭代原数组，生成新数组
5. 实例方法 `join` 数组单元素拼接成了符串
6. 实例方法 `concat`  合并两个数组，生成新数组
7. 实例方法 `sort` 对原数组单元值排序
8. 实例方法 `splice` 删除或替换原数组单元
9. 实例方法 `indexOf` 检索数组单元值
10. 实例方法 `reverse` 反转数组
11. 静态方法 `from` 伪数组转成数组

##### RegExp

`RegExp` 内置的构造函数，用于创建正则表达式。

Regular Expression

```html
<script>
  // 构造函数创建正则
  let reg = new RegExp('\d', 'i');

  // 字面量方式创建正则
  // let reg = /(\d)/i;

  reg.exec('123');
</script>
```

总结：

1. 推荐使用字面量定义正则表达式，而不是 `RegExp` 构造函数
2. `RegExp` 静态属性 $1、$2、$3、... 获取正则分组单元

补充：当使用构造函数创建正则时有两种写法：

```html
<script>
  // 使用 // 定义正则
  let reg = new RegExp(/\d/);
  
  // 或者使用 '' 定义正则
  // 如果使用引号定义正则时，\d、\s、\w
    需要多添加一个 \
  let reg1 = new RegExp('\\d');
</script>
```

#### 包装类型

在 JavaScript 中的字符串、数值、布尔具有对象的使用特征，如具有属性和方法，如下代码举例：

```html
<script>
  // 字符串类型
  let str = 'hello world!';
 	// 统计字符的长度（字符数量）
  console.log(str.length);
  
  // 数值类型
  let price = 12.345;
  // 保留两位小数
  price.toFixed(2);
</script>
```

之所以具有对象特征的原因是字符串、数值、布尔类型数据是 JavaScript 底层使用 Object 构造函数“包装”来的，被称为包装类型。

##### String

`String` 是内置的构造函数，用于创建字符串。

```html
<script>
  // 使用构造函数创建字符串
  let str = new String('hello world!');

  // 字面量创建字符串
  let str2 = '你好，世界！';

  // 检测是否属于同一个构造函数
  console.log(str.constructor === str2.constructor); // true
  console.log(str instanceof String); // false
</script>
```

总结：

1. 推荐使用字面量方式声明字符串，而不是 `String` 构造函数
2. 实例属性 `length` 用来获取字符串的度长
3. 实例方法 `split` 用来将字符串拆分成数组
4. 实例方法 `toUpperCase` 用于将字母转换成大写
5. 实例方法 `toLowerCase` 用于将字母转换成小写
6. 实例方法 `slice` 用于字符串截取
7. 实例方法 `indexOf`  检测是否包含某字符
8. 实例方法 `startsWith` 检测是否以某字符开头
9. 实例方法 `endsWith` 检测是否以某字符结尾
10. 实例方法 `replace` 用于替换字符串，支持正则匹配
11. 实例方法  `padStart` 固定长度字符开始位置打补丁
12. 实例方法  `padEnd` 固定长度字符结束位置打补丁
13. 实例方法 `match` 用于查找字符串，支持正则匹配

注：String 也可以当做普通函数使用，这时它的作用是强制转换成字符串数据类型。

##### Number

`Number` 是内置的构造函数，用于创建数值。

```html
<script>
  // 使用构造函数创建数值
  let x = new Number('10');
  let y = new Number(5);

  // 字面量创建数值
  let z = 20;

  // 检测是否属于同一个构造函数
  console.log(x.constructor === z.constructor);
</script>
```

总结：

1. 推荐使用字面量方式声明数值，而不是 `Number` 构造函数
2. 实例方法 `toFixed` 用于设置保留小数位的长度

注：Number 也可以当做普通函数使用，这时它的作用是强制转换成数值数据类型。

##### Boolean

`Boolean` 是内置的构造函数，用于创建布尔值。

```html
<script>
  // 使用构造函数创建布尔类型
  let locked = new Boolean('10');

  // 字面量创建布尔类型
  let flag = true;

  // 检测是否属于同一个构造函数
  console.log(locked.constructor === flag.constructor);
</script>
```

总结：

1. 推荐使用字面量方式声明布尔值，而不是 `Boolean` 构造函数

注：Boolean 也可以当做普通函数使用，这时它的作用是强制转换成布尔类型数据，由其它数据类型转换成布尔类型的数据被称为真值（truly）或假值（falsly）。

#### 写在最后

至此对 JavaScript 有了更深的理解，即 JavaScript 中一切皆为对象，还有以前学习的 window、Math 对象，最后补充一点无论是引用类型或是包装包类型都包含两个公共的方法 `toString` 和 `valueOf`

```html
<script>
  // 对象类型数据
  let user = {name: '小明', age: 18}
  // 数值类型	 
  let num = 12.345;
  // 字符串类型
  let str = 'hello world!';
  
  str.valueOf(); // 原始值
  user.toString(); // 表示该对象的字符串
</script>
```

总计：

1. `valueOf` 方法获取原始值，数据内部运算的基础，很少主动调用该方法
2. `toString` 方法以字符串形式表示对象



## 面向对象编程：封装、继承、多态

面向对象编程是一种程序设计思想，它具有 3 个显著的特征：封装、继承、多态。

### 封装

封装的本质是将具有关联的代码组合在一起，其优势是能够保证代码复用且易于维护，函数是最典型也是最基础的代码封装形式，面向对象思想中的封装仍以函数为基础，但提供了更高级的封装形式。

#### 命名空间

先来回顾一下以往代码封装的形式：

```html
<script>
  // 普通对象（命名空间）形式的封装
  let beats = {
    name: '狼',
    setName: function (name) {
      this.name = this.name;
    },
    getName() {
      console.log(this.name);
    }
  }

  beats.setName('熊');
  beats.getName();
</script>
```

以往以普通对象（命名空间）形式封装的代码只是单纯把一系列的变量或函数组合到一起，所有的数据变量都被用来共享（使用 this 访问）。

#### 构造函数

对比以下通过面向对象的构造函数实现的封装：

```html
<script>
  function Person() {
    this.name = '佚名';
    // 设置名字
    this.setName = function (name) {
      this.name = name;
    }
    // 读取名字
    this.getName = () => {
      console.log(this.name);
    }
  }

  // 实例对像，获得了构造函数中封装的所有逻辑
  let p1 = new Person();
  p1.setName('小明');
  console.log(p1.--name);// 小明

  // 实例对象
  let p2 = new Person();
  console.log(p2.name); // 佚名
</script>
```

构造函数相当于一个"模子"，能够像字面量那样创建出对象来，所不同的是借助构造函数创建出来的实例对象之间是**彼此不影响**的。

总结：

1. 构造函数体现了面向对象的封装特性
2. 构造函数实例创建的对象彼此独立、互不影响
3. 命名空间式的封装无法保证数据的独立性

注：可以举一些例子，如女娲造人等例子，加深对构造函数的理解。

#### 原型对象

实际上每一个构造函数都有一个名为 `prototype` 的属性，译成中文是原型的意思，`prototype` 的是对象类据类型，称为构造函数的原型对象，每个原型对象都具有 `constructor` 属性代表了该原型对象对应的构造函数。

```html
<script>
  function Person() {
    
  }

  // 每个函数都有 prototype 属性
  console.log(Person.prototype);
</script>
```

如下图所示：

![prototype](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day3\笔记2\笔记\assets\prototype_1.png)

了解了 JavaScript 中构造函数与原型对象的关系后，再来看原型对象具体的作用，如下代码所示：

```html
<script>
  function Person() {
    // 此处未定义任何方法
  }

  // 为构造函数的原型对象添加方法
  Person.prototype.sayHi = function () {
    console.log('Hi~');
  }
	
  // 实例化
  let p1 = new Person();
  p1.sayHi(); // 输出结果为 Hi~
</script>
```

其结构如图所示：

![prototype_2](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day3\笔记2\笔记\assets\prototype_2.png)

构造函数 `Person` 中未定义任何方法，这时实例对象调用了原型对象中的方法 `sayHi`，接下来改动一下代码：

```html
<script>
  function Person() {
    // 此处定义同名方法 sayHi
    this.sayHi = function () {
      console.log('嗨!');
    }
  }

  // 为构造函数的原型对象添加方法
  Person.prototype.sayHi = function () {
    console.log('Hi~');
  }

  let p1 = new Person();
  p1.sayHi(); // 输出结果为 嗨!
</script>
```

![prototype_3](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day3\笔记2\笔记\assets\prototype_3.png)

构造函数 `Person` 中定义与原型对象中相同名称的方法，这时实例对象调用则是构造函中的方法 `sayHi`。

通过以上两个简单示例不难发现 JavaScript 中对象的工作机制：**当访问对象的属性或方法时，先在当前实例对象是查找，然后再去原型对象查找，并且原型对象被所有实例共享。**

```html
<script>
  function Person() {
    // 此处定义同名方法 sayHi
    this.sayHi = function () {
      console.log('嗨!' + this.name);
    }
  }

  // 为构造函数的原型对象添加方法
  Person.prototype.sayHi = function () {
    console.log('Hi~' + this.name);
  }
  // 在构造函数的原型对象上添加属性
  Person.prototype.name = '小明';

  let p1 = new Person();
  p1.sayHi(); // 输出结果为 嗨!
  
  let p2 = new Person();
  p2.sayHi();
</script>
```

![prototype_4](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day3\笔记2\笔记\assets\prototype_4.png)

 什么是原型对象？？

 答：是构造函数的一个属性，它的数据类型是对象

 原型对象有啥用？？

 答：原型对象对应的构造函数的实例方法或属性不存在时会去查找原型对象

总结：**结合构造函数原型的特征，实际开发重往往会将封装的功能函数添加到原型对象中。**

### 继承

继承是面向对象编程的另一个特征，通过继承进一步提升代码封装的程度，JavaScript 中大多是借助原型对象实现继承的特性。

龙生龙、凤生凤、老鼠的儿子会打洞描述的正是继承的含义，分别封装中国人和日本人的行为特征来理解编程中继承的含义，代码如下：

```html
<script>
  // 封装中国人的行为特征
  function Chinese() {
    // 中国人的特征
    this.arms = 2;
    this.legs = 2;
    this.eyes = 2;

    this.skin = 'yellow';
    this.language = '中文';

    // 中国人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }

  // 封装日本人的行为特征
  function Japanese() {
    // 日本人的特征
    this.arms = 2;
    this.legs = 2;
    this.eyes = 2;

    this.skin = 'yellow';
    this.language = '日文';

    // 日本人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }
</script>
```

其实我们都知道无论是中国人、日本人还是其它民族，人们的大部分特征是一致的，然而体现在代码中时人的相同的行为特征被重复编写了多次，代码显得十分冗余，我们可以将重复的代码抽离出来：

#### 原型继承

基于构造函数原型对象实现面向对象的继承特性。

```html
<script>
  // 所有人
  function Person() {
    // 人的特征
    this.arms = 2;
    this.legs = 2;
    this.eyes = 2;
    
    // 人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }
  
  // 中国人
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
  // 日本人
  function Japanese() {
    this.skin = 'yellow';
    this.language = '日文';
  }
</script>
```

上述代码可以理解成将 `Chinese` 和 `Japanese` 共有的属性和方法提取出来了，也就是说 `Chinese` 和 `Japanese` 需要【共享】一些属性和方法，而原型对象的属性和方法恰好是可以被用来共享的，因此我们看如下代码：

```html
<script>
  // 中国人
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
  // 日本人
  function Japanese() {
    this.skin = 'yellow';
    this.language = '日文';
  }
  
  // 人们【共有】的行为特征
  let people = {
    // 人的特征
    arms: 2,
    legs: 2,
    eyes:2,
    // 人的行为
    walk: function () {},
    sleep: function () {},
    sing: function () {}
  }
  
  // 为 prototype 重新赋值
  Chinese.prototype = people;
  Chinese.prototype.constructor = Chinese;
</script>
```

如下图所示：

![prototype_5](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day3\笔记2\笔记\assets\prototype_5.png)

创建对象 `people` 将公共的的属性和方法独立出来，然后赋值给构造函数的 `prototype` 这样无论有多少个民族都可以共享公共的属性和方法了：

```html
<script>
  // 人们【共有】的行为特征
  let people = {
    // 人的特征
    arms: 2,
    legs: 2,
    eyes:2,
    // 人的行为
    walk: function () {},
    sleep: function () {},
    sing: function () {}
  }
  
  // 中国人
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
  // 日本人
  function Japanese() {
    this.skin = 'yellow';
    this.language = '日文';
  }
  
  function Englist() {
    this.skin = 'white';
    this.language= '英文';
  }
  
  // 中国人
  Chinese.prototype = people;
  Chinese.prototype.constructor = Chinese;
  
  let c1 = new Chinese();
 	
  // 日本人
  Japanese.prototype = people;
  Janpanese.prototype.constructor = Japanese;
  // 英国人
  English.prototype = people;
  English.prototype.constructor = English;
  
  // ...
</script>
```

继承是一种可以“不劳而获”的手段！！！上述代码中 `Chinese`、`Japanese`、`English` 都轻松的获得了 `people` 的公共的方法和属性，我们说 `Chinese`、`Japanese`、`English` 继承了 `people`。

#### 构造函数

上述代码中是以命名空间的形式实现的继承，事实上 JavaScript 中继承更常见的是借助构造函数来实现：

```html
<script>
  // 所有人
  function Person() {
    // 人的特征
    this.arms = 2;
    this.legs = 2;
    this.eyes = 2;

    // 人的行为
    this.walk = function () {}
    this.sing = function () {}
    this.sleep = function () {}
  }

  // 封装中国人的行为特征
  function Chinese() {
    // 中国人的特征
    this.skin = 'yellow';
    this.language = '中文';
  }

  // 封装日本人的行为特征
  function Japanese() {
    // 日本人的特征
    this.skin = 'yellow';
    this.language = '日文';
  }

  // human 是构造函数 Person 的实例
  let human = new Person();

  // 中国人
  Chinese.prototype = new Person();
  Chinese.prototype.constructor = Chinese;
  // 日本人
  Japanese.prototype = human;
  Japanese.prototype.constructor = Japanese;
</script>
```

如下图所示：

![prototype_6](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day3\笔记2\笔记\assets\prototype_6.png)

#### 原型链

基于原型对象的继承使得不同构造函数的原型对象关联在一起，并且这种关联的关系是一种链状的结构，我们将原型对象的链状结构关系称为原型链，如下图所示：

![prototype_7](D:\桌面\黑马2022讲义\003- JavaScript基础到高级\JavaScript高级课程资料\day3\笔记2\笔记\assets\prototype_7.png)

```html
作用：用于查找成员提供机制

<script>
  // Person 构造函数
  function Person() {
    this.arms = 2;
    this.walk = function () {}
  }
	
  // Person 原型对象
  Person.prototype.legs = 2;
  Person.prototype.eyes = 2;
  Person.prototype.sing = function () {}
  Person.prototype.sleep = function () {}
	
  // Chinese 构造函数
  function Chinese() {
    this.skin = 'yellow';
    this.language = '中文';
  }
	
  // Chinese 原型对象
  Chinese.prototype = new Person();
  Chinese.prototype.constructor = Chinese;
	
  // 实例化
  let c1 = new Chinese();
  console.log(c1);
</script>
```

在 JavaScript 对象中包括了一个非标准备的属性 `__proto__` 它指向了构造函数的原型对象，通过它可以清楚的查看原型对象的链状结构。

## class - ES6

传统面向对象的编程序语言都是【类】的概念，对象都是由类创建出来，然而早期 JavaScript 中是没有类的，面向对象大多都是基于构造函数和原型实现的，但是 ECMAScript 6  规范开始增加了【类】相关的语法，使得 JavaScript 中的面向对象实现方式更加标准。



### 封装

class（类）是 ECMAScript 6 中新增的关键字，专门用于创建类的，类可被用于实现逻辑的封装。

```html
<script>
  // 创建类
  class Person {
	// 此处编写封装逻辑
  }

  // 实例化
  let p1 = new Person();
  console.log(p1);
</script>
```

#### 实例成员

```html
<script>
  // 创建类
  class Person {
    // 实例属性
    name = '小明';

    // 实例方法
    sleep () {
      console.log('sleeping...')
    }
  }

  // 实例化
  let p1 = new Person();
  p1.sayHi();
</script>
```

总结：

- 关键字 `class` 封装了所有的实例属性和方法

- 类中封装的并不是变量和函数，因此不能使用关键字 `let`、`const` 或 `var`

#### 静态成员

```html
<script>
  // 创建类
  class Person {
    // 静态属性
    static version = '1.0.0';

    // 静态方法
    static getVersion = function () {
      console.log(this.version);
    }
  }
	
  // 静态方法直接访问
  console.log(Person.version);
  Person.getVersion();
</script>
```

总结：

- `static` 关键字用于声明静态属性和方法
- 静态属性和方法直接通过类名进行访问

#### 构造函数

创建类时在类的内部有一个特定的方法 `constructor` ，该方法会在类被实例化时自动被调用，常被用于处理一些初始化的操作。

```html
<script>
  class Person {
    // 实例化时 立即执行
    // 构造函数、构造方法、构造器
    constructor (name, age) {
      this.name = name;
      this.age = age;
    }
	// 实例方法
    walk () {
      console.log(this.name + '正在走路...');
    }
  }
	
  // 实例化
  let p1 = new Person('小明', 18);
  p1.walk();
</script>
```

总结：

- `constructor` 是类中固定的方法名
- `constructor` 方法在实例化时立即执行
- `constructor` 方法接收实例化时传入的参数
- `constructor` 并非是类中必须要存在的方法

### 继承

#### extends

`extends` 是 ECMAScript 6 中实现继承的简洁语法，代码如下所示：

```html
<script>
  class Person {
    // 父类的属性
    legs = 2;
    arms = 2;
    eyes = 2;
		// 父类的方法
    walk () {
      console.log('人类都会走路...');
    }
		// 父类的方法
    sleep () {
      console.log('人都得要睡觉...');
    }
  }

  // Chinese 继承了 Person 的所有特征
  class Chinese extends Person {}

  // 实例化
  let c1 = new Chinese();
  c1.walk();
</script>
```

如上代码所示 `extends` 是专门用于实现继承的语法关键字，`Person` 称为父类、`Chinese` 称为子类。

#### super

在继承的过程中子类中 `constructor` 中必须调 `super` 函数，否则会有语法错误，如下代码所示：

```html
<script>
  class Person {
    // 构造函数
    constructor (name, age) {
      this.name = name;
      this.age = age;
    }
    
    // 父类的属性
    legs = 2;
  	arms = 2;
  
    walk () {
      console.log('人类都会走路...');
    }
  }

  // 子类 English 继承了父类 Person
  class English extends Person {
    // 子类的构造函数
    constructor (name, age) {
      super(name, age);
    }

    // 子类的属性
    skin = 'white';
  	language = '英文';
    }

  // 实例化
  let e1 = new English('jack', 18);
  console.log(e1.name);
</script>
```

子类构造函数中的 `super` 函数的作用是可以将子类实例化时获得的参数传入父类的构造函数之中。



## 扩展：数组filter、map、如reduce

### 含义

#### Filter

你想将一个数组做一个过滤，按照某规则提取出一些特定的项来。结果也生成了一个新数组，但新数组的长度小于或等于原数组。

#### Map

你想将一个数组中的每一项做个转换，结果生成了一个新数组，并且新数组的长度和原数组一致。

#### Reduce

你想通过遍历一个数组的每一项来计算出一个新东西，最后生成的结果可以是任何类型的变量：一个新数组，一个新对象，一个新布尔值…

#### 共同点

`filter`、`map`和`reduce`共同点就是并不会对原数组做任何改动，结果都是生成一个新变量。让我们来看一些例子：



### 使用方式

#### filter

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<!-- filter: 
    你想将一个数组做一个过滤，按照某规则提取出一些特定的项来。
    结果也生成了一个新数组，但新数组的长度小于或等于原数组。 -->

<body>
    <script>
        // 现在我们有一个对象列表，其中每个对象代表的是一种变形金刚
        var transformers = [
            {
                name: 'Optimus Prime',
                form: 'Freightliner Truck',
                team: 'Autobot'
            },
            {
                name: 'Megatron',
                form: 'Gun',
                team: 'Decepticon'
            },
            {
                name: 'Bumblebee',
                form: 'VW Beetle',
                team: 'Autobot'
            },
            {
                name: 'Soundwave',
                form: 'Walkman',
                team: 'Decepticon'
            }
        ];
        // filter语法
        //  callback将数组元素当参数传入，并返回一个布尔值。
        // 当返回值为真时，该元素被加入新数组中，反之则被过滤掉。
        // Array.prototype.filter(callback(item));

        // 现在我们把这些变形金刚过滤出汽车人(Autobots)
        // 方法一：箭头函数
        var autobots = transformers.filter((item) => item.team === 'Autobot')
        console.log('方法一：箭头函数 autobots', autobots);
        // 方法二：匿名函数
        var autobots = transformers.filter(function (item)  {
            return item.team === 'Autobot'
        })
        console.log('方法二：匿名函数 autobots', autobots);
        // 方法三：命名函数
        function isAutobot(transformer) {
            return transformer.team === 'Autobot';
        }
        var autobots = transformers.filter(isAutobot);
        console.log('方法三：命名函数 autobots', autobots);
    </script>
</body>

</html>
~~~



### map

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<!-- map: 
    你想将一个数组中的每一项做个转换，
    结果生成了一个新数组，并且新数组的长度和原数组一致。 -->

<body>
    <script>
        // 现在我们有一个对象列表，其中每个对象代表的是一种变形金刚
        var transformers = [
            {
                name: 'Optimus Prime',
                form: 'Freightliner Truck',
                team: 'Autobot'
            },
            {
                name: 'Megatron',
                form: 'Gun',
                team: 'Decepticon'
            },
            {
                name: 'Bumblebee',
                form: 'VW Beetle',
                team: 'Autobot'
            },
            {
                name: 'Soundwave',
                form: 'Walkman',
                team: 'Decepticon'
            }
        ];

        // 如果想得到一个包含所有变形（form）的列表该怎么做呢？
        // map使用语法：	
        // Array.prototype.map(callback(item));
        // callback会遍历数组的每个元素，在其中返回的每一个数值将会组成新数组。

        // 方法一：箭头函数
        let robotsInDisguise1 = transformers.map((item) => item.form)
        console.log('方法一：箭头函数 ', robotsInDisguise1);
        // 方法二：匿名函数

        let robotsInDisguise2 = transformers.map(function (transformer) {
            return transformer.form;
        });
        console.log('方法二：匿名函数 ', robotsInDisguise2);
        // 方法三：命名函数
        function getForm(transformer) {
            return transformer.form;
        }
        let robotsInDisguise3 = transformers.map(getForm);
        console.log('方法三：命名函数 ', robotsInDisguise3);

    </script>
</body>

</html>
~~~





### reduce

~~~html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<!-- filter: 
    你想通过遍历一个数组的每一项来计算出一个新东西，
    最后生成的结果可以是任何类型的变量：
    一个新数组，一个新对象，一个新布尔值… -->

<body>
    <script>
        // 现在我们有一个对象列表，其中每个对象代表的是一种变形金刚

        var constructicons = [
            {
                name: 'Scrapper',
                form: 'Freightliner Truck',
                team: 'Decepticon',
                bodyPart: 'rightLeg'
            },
            {
                name: 'Hook',
                form: 'Mobile Crane',
                team: 'Decepticon',
                bodyPart: 'upperTorso'
            },
            {
                name: 'Bonecrusher',
                form: 'Bulldozer',
                team: 'Decepticon',
                bodyPart: 'leftArm'
            },
            {
                name: 'Scavenger',
                form: 'Excavator',
                team: 'Decepticon',
                bodyPart: 'rightArm'
            },
            {
                name: 'Mixmaster',
                form: 'Concrete Mixer',
                team: 'Decepticon',
                bodyPart: 'leftLeg'
            },
            {
                name: 'Long Haul',
                form: 'Dump Truck',
                team: 'Decepticon',
                bodyPart: 'lowerTorso'
            }
        ];
        //         Array.reduce()是通过遍历每个数组元素对其做运算，
        //         因此，也是三个API之间最富有技巧的，其使用语法也略显复杂：

        // Array.prototype.reduce(callback(previousValue, currentValue[, index], array]), initialValue)
        // educe的callback需要至少两个参数。第一个是从上次遍历中返回的一个值，第二个是当前数组遍历到的一个值
        // ，返回值又将当做第一个参数被传入到下一次遍历。

        // 组合变形金刚

        function assemble(combiner, transformer) {
            //每次遍历都会将当前的变形金刚（transformer）的名字加入到组合器（combiner）的form中去。
            combiner.form[transformer.bodyPart] = transformer.name;
            // bodyPart:name
            return combiner;
        }
        var devastator = constructicons.reduce(assemble, {
            name: 'Devastator',
            team: 'Decepticon',
            form: {}
        });
        console.log('reduce', devastator);
        /*
            devastator == {
            name: ‘Devastator’,
            team: ‘Decepticon’,
            form: {
                leftArm: "Bonecrusher"
                leftLeg: "Mixmaster"
                lowerTorso: "Long Haul"
                rightArm: "Scavenger"
                rightLeg: "Scrapper"
                upperTorso: "Hook"
            }
        }        
*/
    </script>
</body>

</html>
~~~

