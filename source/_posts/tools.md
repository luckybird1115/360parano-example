---
title: 常用工具
date: 2019-03-18 12:46:05
tags: 
    - tools
categories: 工具  
---
主要介绍doctoc插件自动生成文章目录
<!--more-->
# markdown相关
## doctoc(markdown目录自动生成)
GitHub中的markdown文件直接写[TOC]是无法生成目录的，可以使用工具doctoc
npm install -g doctoc
使用方式
+ 对当前文件夹中所有文件生成目录  
  e.g.
    ```
    cd D:\Develop\Documents\Notes\CSDN笔记\temp
    doctoc .
    ``` 

+ 对文件夹中单个文件生成目录（文件名中间不能有空格）  
`doctoc /path/to/file [...]`  
  如：
    ```
    doctoc README.md   
    doctoc CONTRIBUTING.md LICENSE.md 
    doctoc D:\Develop\Documents\Notes\CSDN笔记\JAVARxJava.md
   ```
