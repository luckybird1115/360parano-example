---
title: 前端基础复习-JavaScript-3
date: 2020-04-04 23:04:57
tags: js基础
categories: 前端
---
前端自检清单JavaScript基础3--作用域和闭包

<!--more-->
### 作用域和闭包

#### 1.理解词法作用域和动态作用域

词法作用域是由写代码时将变量和块作用域写在哪里决定的，函数的词法作用域由函数声明所处位置决定的

查找 词法作用域查找只会查找一级标识符

遮蔽 从内逐级向外查找作用域，直到遇到第一个匹配的标识符为止，在多层嵌套作用域内定义同名标识符，产生遮蔽效应

动态作用域是运行时确定的，关注函数从何处调用

注js只有词法作用域，只是this的某种机制像动态作用域

```
function foo(){
    console.log(a);//3(而不是2！)//动态作用域是应该是3，但是运行js代码输出的还是2
}
function bar(){
    var a=3;
    foo();
}
var a=2;
bar();
```



#### 2.理解`JavaScript`的作用域和作用域链

全局作用域

局部作用域

全局变量的作用域是全局的，在代码的任何地方都是有定义的。然而函数的参数和局部变量只在函数体内有定义。另外局部变量的优先级要高于同名的全局变量,也就是说当局部变量与全局变量重名时，局部变量会覆盖全局变量。

作用域链

作用域链就是由多个作用域组成的，当在自身作用域内找不到该变量的时候，会沿着作用域链逐步向上查找，若在全局作用域内部仍找不到该变量，则会抛出异常。

#### 3.理解`JavaScript`的执行上下文栈，可以应用堆栈信息快速定位问题

执行期上下文

当**函数执行**时（准确来说，是在函数发生预编译的前一刻），会创建一个执行期上下文的内部对象。一个执行期上下文定义了一个函数执行时的环境。

每调用一次函数，就会创建一个新的上下文对象，他们之间是相互独立且独一无二的。当函数执行完毕，它所产生的执行期上下文会被销毁。

#### 4.`this`的原理以及几种不同使用场景的取值

this代表函数调用相关联的对象，通常称之为执行上下文。

1. 作为函数直接调用，非严格模式下，this指向window，严格模式下，this指向undefined；
2. 作为某对象的方法调用，this通常指向调用的对象。
3. 使用apply、call、bind 可以绑定this的指向。
4. 在构造函数中，this指向新创建的对象
5. 箭头函数没有单独的this值，this在箭头函数创建时确定，它与声明所在的上下文相同。

#### 5.闭包的实现原理和作用，可以列举几个开发中闭包的实际应用

闭包的概念：指有权访问另一个函数作用域中的变量的函数，一般情况就是在一个函数中包含另一个函数。

闭包的作用：访问函数内部变量、保持函数在环境中一直存在，不会被垃圾回收机制处理

产生闭包：

将一个函数作为另一个函数的返回值

将函数作为实参传递给另一个函数调用

变量私有化

应用：防抖函数，计数器

#### 6.理解堆栈溢出和内存泄漏的原理，如何防止

**内存泄漏**：**占用的内存**没有及时释放。

注意，内存泄露的次数积累多了，就容易导致内存溢出。

**常见的内存泄露**：

- 1.意外的全局变量

- 2.没有及时清理的计时器或回调函数

- 3.闭包

#### 7.如何处理循环的异步操作

for循环

递归处理异步函数

#### 8.理解模块化解决的实际问题，可列举几个模块化方案并理解其中原理

**CommonJS规范**，NodeJS使用，加载时运行，输出值的拷贝，会缓存结果，模块可多次加载，但只有第一次加载运行一次

**AMD**，Asynchronous Moudle Definition “异步模块定义”，需要在声明的时候指定所有的依赖，通过形参传递依赖到模块内容中，特点：提前执行（异步加载依赖先执行）+延迟执行

```
define(id?, dependencies, factory);
//使用
require('id')
```

**CMD**，与AMD主要区别在于定于模块与依赖引入部分。支持动态引入，特点：延迟执行（运行到需加载，根据顺序执行）

```
define(funtion(require, exports, moudle) {}

require
require.async
```

**ES6 Module** 编译时运行，输出值的引用