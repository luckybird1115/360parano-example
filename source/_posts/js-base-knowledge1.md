---
title: 前端基础复习-JavaScript-1
date: 2020-04-03 18:36:11
tags: js基础
categories: 前端
---
前端自检清单JavaScript基础1--变量和类型
<!--more-->

### 变量和类型

#### 1.`JavaScript`规定了几种语言类型

7种，

undefined,null,Number,Boolean,

String,Object,Symbol

#### 2.`JavaScript`对象的底层数据结构是什么

Null (js中的数据在底层是以二进制存储，如果前三位为0，那么就会判定为object，而null的所有都为0)

**js基本类型数据都是直接按值存储在栈中的**(Undefined、Null、不是new出来的布尔、数字和字符串)

**js引用类型数据被存储于堆中** (如对象、数组、函数等，它们是通过拷贝和new出来的），引用类型的数据的地址指针存储于栈中，地址指针指向堆中的数据

Object>HeapObject（存储）>JSReceiver(用于原型查找)->(FixedArrayBase->FixedArray)+JSObject->JSArray+JSCollection+(JSFunction>JSMap+JSSet)

#### 3.`Symbol`类型在实际开发中的应用、可手动实现一个简单的`Symbol`

应用场景：1.使用Symbol来作为对象属性名（key）

2.使用Symbol来替代常量

3.使用Symbol定义类的私有属性和方法

symbol 类型的key不能被Object.keys和for---of

#### 4.`JavaScript`中的变量在内存中的具体存储形式

`JavaScript` 中的变量分为基本类型和引用类型:

基本类型: 保存在栈内存中的简单数据段，它们的值都有固定的大小，保存在栈空间，通过按值访问

引用类型: 保存在堆内存中的对象，值大小不固定，栈内存中存放的该对象的访问地址指向堆内存中的对象，`JavaScript` 不允许直接访问堆内存中的位置，因此操作对象时，实际操作对象的引用

#### 5.基本类型对应的内置对象，以及他们之间的装箱类型的拆箱操作

String，Number，Boolean

```
// 隐式装箱： 每当读取一个基本类型的值时，后台会创建一个该基本类型所对应的对象。
  // 在这个基本类型上调用方法，其实是在这个基本类型对象上调用方法。
  // 这个基本类型的对象是临时的，它只存在于方法调用那一行代码执行的瞬间，执行方法后立刻被销毁。
  let num=123;
  num.toFixed(2); // '123.00'//上方代码在后台的真正步骤为
  var c = new Number(123);
  c.toFixed(2);
  c = null;
  // 显式装箱: 通过内置对象 Boolean、Object、String 等可以对基本类型进行显示装箱。
  var obj = new String('123');
```

#### 6.理解值类型和引用类型

赋值传引用地址，值类型直接赋值

#### 7.`null`和`undefined`的区别

undefined类型表示未定义，值只有一个undefined，

undefined是一个变量，而非关键字。

而Null只有一个值，null，表示空值，是关键字，可以通过null关键字来获取null值

#### 8.至少可以说出三种判断`JavaScript`数据类型的方式，以及他们的优缺点，如何准确的判断数组类型

typeof 

instanceof

constructor

Object,prototype.toString.call([])=='[object Array]'

Array.isArray()

#### 9.可能发生隐式类型转换的场景以及转换原则，应如何避免或巧妙应用

- isNaN ()
- 自增/自减运算符：`++`、`—-`
- 正号/负号：`+a`、`-a`
- 加号：`+`
- 运算符：`-`、`*`、`/`
- ==在不同类型时进行隐式类型转换
  - 会先判断是否在对比 null 和 undefined，是的话就会返回 true
  - 判断两者类型是否为 string 和 number，是的话就会将字符串转换为 number
  - 判断其中一方是否为 boolean，是的话就会把 boolean 转为 number 再进行判断
  - 判断其中一方是否为 object 且另一方为 string、number 或者 symbol，是的话就会把 object 转为原始类型再进行判断
  - 两边都是对象的话，那么只要不是同一对象的不同引用，都为false

- 逻辑运算符：`&&`、`||`、`！` 。非布尔值进行**与或**运算时，会先将其转换为布尔值，然后再运算，但运算结果是**原值**。具体可以看下一篇文章《运算符》。

- 关系运算符：`<`、`>` `<=` `>=`等。关系运算符，得到的运算结果都是布尔值：要么是true，要么是false。

#### 10.出现小数精度丢失的原因，`JavaScript`可以存储的最大数字、最大安全数字，`JavaScript`处理大数字的方法、避免精度丢失的方法

- 精度丢失原因，说是 `JavaScript` 使用了 `IEEE 754` 规范，二进制储存十进制的小数时不能完整的表示小数
- 能够表示的最大数字 `Number.MAX_VALUE` 等于 `1.7976931348623157e+308` ,最大安全数字 `Number.MAX_SAFE_INTEGER` 等于 `9007199254740991`
- 避免精度丢失
  - 计算小数时，先乘 `100` 或 `1000`，变成整数再运算
  - 如果值超出了安全整数，有一个最新提案，`BigInt` 大整数，它可以表示任意大小的整数，注意只能表示整数，而不受安全整数的限制
