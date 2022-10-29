---
title: 前端基础复习-JavaScript-5
date: 2020-04-04 23:07:00
tags: js基础
categories: 前端
---
前端自检清单JavaScript基础5--语法和API

<!--more-->
### 语法和API

#### 1.理解`ECMAScript`和`JavaScript`的关系

`JavaScript`包括核心语法，DOM，BOM，而核心语法实际上就是指ECMAScript，而JS又是不断在发展的, 而这个发展实际上最主要的就是ECMAScript版本的更新, 而DOM 和 BOM的更新较少, 因此, 可以认为 ECMAScript是JavaScript的参照, 而JavaScript是ECMAScript的一个实现. 

例如ECMAScript2015简称ES6

#### 2.熟练运用`es5`、`es6`提供的语法规范

#### 3.熟练掌握`JavaScript`提供的全局对象（例如`Date`、`Math`）、全局函数（例如`decodeURI`、`isNaN`）、全局属性（例如`Infinity`、`undefined`）

#### 4.熟练应用`map`、`reduce`、`filter` 等高阶函数解决问题

#### 5.`setInterval`需要注意的点，使用`settimeout`实现`setInterval`

回调函数在计时之后才开始执行, 如果主线程执行时间过长，时间间隔未必准确，

```js
function myInterval(callback,delay){
    function interval(){
        setTimeout(interval, delay);
        callback();
      }
      setTimeout(interval, delay)
}
```

#### 6.`JavaScript`提供的正则表达式`API`、可以使用正则表达式（邮箱校验、`URL`解析、去重等）解决常见问题

手机号码正则表达式
正则验证手机号，忽略前面的0，支持130-139，150-159。忽略前面0之后判断它是11位的。

```js
/^0*1(3|5)\d{9}$/
```

使用正则表达式实现删除字符串中的空格

```js
funtion trim(str) {
  let reg = /^\s+|\s+$/g
  return str.replace(reg, '');
}
```

限制文本框只能输入数字和两位小数点等等

```js
/^\d*\.\d{0,2}$/
```

只能输入小写的英文字母和小数点，和冒号，正反斜杠(：./\)

```js
/^[a-z\.:\/\\]*$/
```

替换小数点前内容为指定内容
例如：infomarket.php?id=197 替换为 test.php?id=197

```js
var reg = /^[^\.]+/;
var target = '---------';
str = str.replace(reg, target)
```

只匹配中文的正则表达式

```js
/[\u4E00-\u9FA5\uf900-\ufa2d]/ig
```

返回字符串的中文字符个数
先去掉非中文字符，再返回length属性。

```js
function cLength(str){
  var reg = /[^\u4E00-\u9FA5\uf900-\ufa2d]/g;
  //匹配非中文的正则表达式
  var temp = str.replace(reg,'');
  return temp.length;
}
```

正则表达式取得匹配IP地址前三段
只要匹配掉最后一段并且替换为空字符串就行了

```js
function getPreThrstr(str) {
  let reg = /\.\d{1,3}$/;
  return str.replace(reg,'');
}
```

匹配<ul>与</ul>之间的内容

```js
/<ul>[\s\S]+?</ul>/i
```


用正则表达式获得文件名
c:\images\tupian\006.jpg
可能是直接在盘符根目录下，也可能在好几层目录下，要求替换到只剩文件名。
首先匹配非左右斜线字符0或多个，然后是左右斜线一个或者多个。

```js
function getFileName(str){
  var reg = /[^\\\/]*[\\\/]+/g;
  // xxx\ 或是 xxx/
  str = str.replace(reg,'');
  return str;
}
```


绝对路径变相对路径
"http://23.123.22.12/image/somepic.gif"转换为："/image/somepic.gif"

```js
var reg = /http:\/\/[^\/]+/;
str = str.replace(reg,"");
```


用户名正则
用于用户名注册，，用户名只 能用 中文、英文、数字、下划线、4-16个字符。

```js
/^[\u4E00-\u9FA5\uf900-\ufa2d\w]{4,16}$/
```


匹配英文地址
规则如下:
包含 "点", "字母","空格","逗号","数字"，但开头和结尾不能是除字母外任何字符。

```js
/^[a-zA-Z][\.a-zA-Z,0-9]*[a-zA-Z]$/
```


正则匹配价格
开头数字若干位，可能有一个小数点，小数点后面可以有两位数字。

```js
/^\d+(\.\d{2})?$/
```


身份证号码的匹配
身份证号码可以是15位或者是18位，其中最后一位可以是X。其它全是数字

```js
/^(\d{14}|\d{17})(X|x)$/
```


单词首字母大写
每单词首字大写，其他小写。如blue idea转换为Blue Idea，BLUE IDEA也转换为Blue Idea

```js
function firstCharUpper(str) {
  str = str.toLowerCase();
  let reg = /\b(\w)/g;
  return str.replace(reg, m => m.toUpperCase());
}
```


正则验证日期格式
yyyy-mm-dd格式
4位数字，横线，1或者2位数字，再横线，最后又是1或者2位数字。

```js
/^\d{4}-\d{1,2}-\d{1,2}$/
```


去掉文件的后缀名
www.abc.com/dc/fda.asp 变为 www.abc.com/dc/fda

```js
function removeExp(str) {
  return str.replace(/\.\w$/,'')
}
```


验证邮箱的正则表达式
开始必须是一个或者多个单词字符或者是-，加上@，然后又是一个或者多个单词字符或者是-。然后是点“.”和单词字符和-的组合，可以有一个或者
多个组合。

```js
/^[\w-]+@\w+\.\w+$/
```


正则判断标签是否闭合
例如：<img xxx=”xxx” 就是没有闭合的标签；

<p>p的内容，同样也是没闭合的标签。


标签可能有两种方式闭合，<img xxx="xxx" /> 或者是<p> xxx </p>。

```js
/<([a-z]+)(\s*\w*?\s*=\s*".+?")*(\s*?>[\s\S]*?(<\/\1>)+|\s*\/>)/i
```


正则判断是否为数字与字母的混合
不能小于12位，且必须为字母和数字的混

```js
/^(([a-z]+[0-9]+)|([0-9]+[a-z]+))[a-z0-9]*$/i
```


将阿拉伯数字替换为中文大写形式

```js
function replaceReg(reg,str){
  let arr=["零","壹","贰","叁","肆","伍","陆","柒","捌","玖"];
  let reg = /\d/g;
  return str.replace(reg,function(m){return arr[m];})
}
```

去掉标签的所有属性
<td style="width: 23px; height: 26px;" align="left">***</td>
变成没有任何属性的
<td>***</td>
思路：非捕获匹配属性，捕获匹配标签，使用捕获结果替换掉字符串。正则如下：

#### 7.`JavaScript`异常处理的方式，统一的异常处理方案

Javascript 异常处理的演进https://zhuanlan.zhihu.com/p/25245932

**Promise 异常处理**

不仅是 reject，抛出的异常也会被作为拒绝状态被 Promise 捕获。在微任务中抛出的异常能够被捕获，宏任务中抛出异常无法被捕获，因为已经离开了函数的执行上下文

```js
function fetch(callback) {
    return new Promise((resolve, reject) => {
        throw Error('用户不存在')
    })
}

fetch().then(result => {
    console.log('请求处理', result) // 永远不会执行
}).catch(error => {
    console.log('请求处理异常', error) // 请求处理异常 用户不存在
})
```

```js
function fetch(callback) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
             throw Error('用户不存在')
        })
    })
}

fetch().then(result => {
    console.log('请求处理', result) // 永远不会执行
}).catch(error => {
    console.log('请求处理异常', error) // 永远不会执行
})

// 程序崩溃
// Uncaught Error: 用户不存在
```

Promise 的异常处理有了比较清晰的答案，只要注意在 macrotask 级别回调中使用 reject，就没有抓不住的异常。

**Async Await 异常**

不论是同步、异步的异常，await 都不会自动捕获，但好处是可以自动中断函数，我们大可放心编写业务逻辑，而不用担心异步异常后会被执行引发雪崩.

我们可以使用 try catch 捕获异常。

因为此时的异步其实在一个作用域中，通过 generator 控制执行顺序，所以可以将异步看做同步的代码去编写，包括使用 try catch 捕获异常。

try..catch

但无法捕获异步的错误

##### 业务场景 统一的异常处理方案：

我们来编写类级别装饰器，专门捕获 async 函数抛出的异常：

```text
const asyncClass = (errorHandler?: (error?: Error) => void) => (target: any) => {
    Object.getOwnPropertyNames(target.prototype).forEach(key => {
        const func = target.prototype[key]
        target.prototype[key] = async (...args: any[]) => {
            try {
                await func.apply(this, args)
            } catch (error) {
                errorHandler && errorHandler(error)
            }
        }
    })
    return target
}
```

将类所有方法都用 try catch 包裹住，将异常交给业务方统一的 errorHandler 处理：

```text
const successRequest = () => Promise.resolve('a')
const failRequest = () => Promise.reject('b')

const iAsyncClass = asyncClass(error => {
    console.log('统一异常处理', error) // 统一异常处理 b
})

@iAsyncClass
class Action {
    async successReuqest() {
        const result = await successRequest()
        console.log('successReuqest', '处理返回值', result)
    }

    async failReuqest() {
        const result = await failRequest()
        console.log('failReuqest', '处理返回值', result) // 永远不会执行
    }

    async allReuqest() {
        const result1 = await successRequest()
        console.log('allReuqest', '处理返回值 success', result1)
        const result2 = await failRequest()
        console.log('allReuqest', '处理返回值 success', result2) // 永远不会执行
    }
}

const action = new Action()
action.successReuqest()
action.failReuqest()
action.allReuqest()
```

我们也可以编写方法级别的异常处理：

```text
const asyncMethod = (errorHandler?: (error?: Error) => void) => (target: any, propertyKey: string, descriptor: PropertyDescriptor) => {
    const func = descriptor.value
    return {
        get() {
            return (...args: any[]) => {
                return Promise.resolve(func.apply(this, args)).catch(error => {
                    errorHandler && errorHandler(error)
                })
            }
        },
        set(newValue: any) {
            return newValue
        }
    }
}
```

业务方用法类似，只是装饰器需要放在函数上：

```text
const successRequest = () => Promise.resolve('a')
const failRequest = () => Promise.reject('b')

const asyncAction = asyncMethod(error => {
    console.log('统一异常处理', error) // 统一异常处理 b
})

class Action {
    @asyncAction async successReuqest() {
        const result = await successRequest()
        console.log('successReuqest', '处理返回值', result)
    }

    @asyncAction async failReuqest() {
        const result = await failRequest()
        console.log('failReuqest', '处理返回值', result) // 永远不会执行
    }

    @asyncAction async allReuqest() {
        const result1 = await successRequest()
        console.log('allReuqest', '处理返回值 success', result1)
        const result2 = await failRequest()
        console.log('allReuqest', '处理返回值 success', result2) // 永远不会执行
    }
}

const action = new Action()
action.successReuqest()
action.failReuqest()
action.allReuqest()
```

在 nodejs 端，记得监听全局错误，兜住落网之鱼：

```text
process.on('uncaughtException', (error: any) => {
    logger.error('uncaughtException', error)
})

process.on('unhandledRejection', (error: any) => {
    logger.error('unhandledRejection', error)
})
```

在浏览器端，记得监听 window 全局错误，兜住漏网之鱼：

```text
window.addEventListener('unhandledrejection', (event: any) => {
    logger.error('unhandledrejection', event)
})
window.addEventListener('onrejectionhandled', (event: any) => {
    logger.error('onrejectionhandled', event)
})
```

