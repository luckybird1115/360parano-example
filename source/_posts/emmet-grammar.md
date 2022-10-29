---
title: emmet-grammar
date: 2020-03-30 11:04:52
tags: 工具
categories: 前端
---

<!--more-->

emmet是一个提高前端开发效率的工具。

### 使用方法

#### 后代>

nav>ul>li

#### 兄弟+

div+p

#### 上级^

div+div>p^span

```html
<div></div>
    <div>
        <p></p>
    </div>
    <span></span>
```

#### 分组()

div>(header>ul>li*2)+content+footer

```html
<div>
        <header>
            <ul>
                <li></li>
                <li></li>
            </ul>
        </header>
        <content></content>
        <footer></footer>
    </div>
```

#### 乘法*

#### 自增符号$

ul>li.item$*5

```html
  <ul>
        <li class="item1"></li>
        <li class="item2"></li>
        <li class="item3"></li>
    </ul>
```

$@- 倒着的

$@x x开头

#### ID和类属性

#xxx, .xxx

form#search.wide

#### 自定义属性

img[data-src="xxx"]

#### 文本

{}  p{click}+a{here}

```html
<p>click</p>
<a href="">here</a>
```

#### HTML

! 

a

a:link

a:mail

link:css

input:password