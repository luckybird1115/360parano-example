---
title: 前端基础复习-JavaScript-4
date: 2020-04-04 23:05:04
tags: js基础
categories: 前端
---
前端自检清单JavaScript基础4--执行机制

<!--more-->
### 执行机制

#### 1.为何`try`里面放`return`，`finally`还会执行，理解其内部机制

当try和finally里都有return时，会忽略try的return，而使用finally的return。

- JVM 采用异常表的方式来处理 try catch 的跳转逻辑
- finally 的实现采用拷贝 finally 语句块的方式来实现 finally 一定会执行的语义逻辑

#### 2.`JavaScript`如何实现异步编程，可以详细描述`EventLoop`机制

一个 Event Loop 中，可以有一个或者多个任务队列(task queue)，一个任务队列便是一系列有序任务(task)的集合；**每个任务都有一个任务源(task source)，源自同一个任务源的 task 必须放到同一个任务队列，从不同源来的则被添加到不同队列。** setTimeout/Promise 等API便是任务源，而进入任务队列的是他们指定的具体执行任务。

浏览器为了能够使得JS内部(macro)task与DOM任务能够有序的执行，**会在一个(macro)task执行结束后，在下一个(macro)task 执行开始前，对页面进行重新渲染**，流程如下：

    (macro)task->渲染->(macro)task->...

microtask（又称为微任务），**可以理解是在当前 task 执行结束后立即执行的任务**。也就是说，在当前task任务后，下一个task之前，在渲染之前，执行所有microtask

*   执行一个宏任务（栈中没有就从事件队列中获取）
*   执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
*   宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
*   当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染
*   渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

#### 3.宏任务和微任务分别有哪些

**宏任务**(macro)task主要包含：script(整体代码)、setTimeout、setInterval、I/O、UI交互事件、postMessage、MessageChannel、setImmediate(Node.js 环境)

**微任务**：Promise.then、MutationObserver(在指定的DOM发生变化时被调用)、process.nextTick(Node.js 环境)

#### 4.可以快速分析一个复杂的异步嵌套逻辑，并掌握分析方法

理解好任务队列，宏任务和微任务

#### 5.使用`Promise`实现串行

在promise中返回一个promise对象就是一个串行。

```js
//实现Promise串行
function makePromise(value){
    return new Promise(resolve=>{
        setTimeout(()=>{
            resolve(value)
        },Math.random()*1000)
    })
}
function print(value){
    console.log(value);
}

let promises = [1, 3, 4, 5, 6].map((item, index) => {
    return makePromise(item)
  });
  // 串行执行
let parallelPromises = promises.reduce(
    (total, currentValue) => total.then(() => currentValue.then(print)),Promise.resolve()
  )
  parallelPromises
.then(() => {
  console.log('total then done')
})
.catch(() => {
  console.log('done')
})
```



#### 6.`Node`与浏览器`EventLoop`的差异

![img](../img/node-libuv.png)

- **timers 阶段**：这个阶段执行timer（`setTimeout`、`setInterval`）的回调

- **I/O callbacks 阶段**：执行一些系统调用错误，比如网络通信的错误回调

- **idle, prepare 阶段**：仅node内部使用

- **poll 阶段**：获取新的I/O事件, 适当的条件下node将阻塞在这里，当已有超时timer，执行它的回调函数

- **check 阶段**：执行 `setImmediate()` 的回调

- **close callbacks 阶段**：执行 `socket` 的 `close` 事件回调

  

浏览器环境下，`microtask`的任务队列是每个`macrotask`执行完之后执行。

而在Node.js中，`microtask`会在事件循环的各个阶段之间执行，也就是一个阶段执行完毕，就会去执行`microtask`队列的任务。

在nodejs中递归的调用`process.nextTick()`会导致I/O starving，官方推荐使用`setImmediate()`

#### 7.如何在保证页面运行流畅的情况下处理海量数据



#### 8.js的单线程模式

单线程，js的主要用途是与用户互动，以及操作DOM，如果使用多线程，容易产生复杂的同步问题。

- 所有的同步任务都在主线程上执行，形成一个执行栈，
- 主线程之外，存在一个任务队列，当异步任务有了运行结果，则会在任务队列中放置一个事件
- 一旦”执行栈”中的所有同步任务执行完毕，系统就会读取”任务队列”，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。

 - 主线程不断重复上面的第三步。