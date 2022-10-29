---
title: QGIS的插件开发（C++&python）
date: 2021-03-12 15:13:25
tags: QGIS
categories: QGIS
---

<!--more-->

# QGIS的插件开发（C++&Python）

## 基于C++

C++插件一般以Dll文件的形式，存放在工程下。当我们使用对应的插件时，会动态加载它。
和Python良好的可移植性不同，Dll文件及其附属一系列的链接库不可以直接加入现有工程。所以，我们采用重新编译的方法来生成对应的Dll文件。

## 研究思路

QGIS是基于QT开发的开源项目。其中自带有Plugin Builder的插件，利用Plugin Builder，可以直接建立一个插件模板。

然而，Plugin Builder生成的插件模板是Python格式，为了让它能够成为我们希望的Dll格式。
我们将利用Plugin Builder的功能，先生成插件模板源码。再将其加入qgis源码中，重新用C++编译整个qgis工程来生成它。
（本文中将用大小写来区分QGIS Desktop桌面应用和qgis源码）

## 源码

【Plugin Builder源码】

https://github.com/g-sherman/Qgis-Plugin-Builder

Plugin Builder是一个同QGIS版本变化同步更新的一个插件。

如果你有下载安装过QGIS Desktop，你会发现他的Plugin Builder插件有所更改，名为Plugin Builder 3
同时在3版本以上的qgis源码中，我们找不到Plugin Builder的源码。
（本文中将用Plugin Builder和Plugin Builder 3分别表示不同版本的PluginBuilder插件）

而目前提供的Plugin Builder（包含CMakeList）源码，还使用早期QT4和Python2的编译规则。
所以，如果你下载的源码版本是较新的（3版本以上），你还需要下载qgis-ltr（稳定维护版本2.18.25）的源码。
qgis-latest-ltr

在这个版本里，你可以在src/plugins下找到我们需要的Plugin Builder源码

**我找不到，单独下载了plugin builder的源码，按照教程放置，提示**找不到resouce

```
Traceback (most recent call last):
  File "plugin_builder.py", line 47, in <module>
    from .resources import *  #pylint: disable=W0401,W0614
ModuleNotFoundError: No module named '__main__.resources'; '__main__' is not a package
```

### 生成插件模板

生成插件指令
在plugin_builder.py所在位置运行cmd

PS D:\QGIS\qgis-3.2.2\src\plugins> python3 plugin_builder.py 
它和MFC类似，是一个简单的向导。
会依次输出要求指令，输入合适的内容即可直接构造。

您的插件MyTool已在my_plugin中创建，CMakeLists.txt已被修改。
一旦您的插件成功构建，请查看my_plugin/readme关于如何开始的提示

#### 生成vs工程

将处理好的源码，利用cmake生成对应的工程文件。
并对其编译，使其成功生成。

在资源管理器中找到以你的插件为命名的项目
如，我的是——mytoolplugin
对这个项目的添加有关.h和.cpp文件，对其编辑，最后生成解决方案中的
即为你的C++插件
你可以在新建项目的目录中找到他们。

出错补充
在加入自定义的插件后，这个插件项目，可能会出现以下错误
C1083 无法打开包括文件: “qgis_core.h”: No such file or directory
C1083 无法打开包括文件: “qgis_gui.h”: No such file or directory
C1083 无法打开包括文件: “qgisexpression.h”: No such file or directory
解决方法——
在这个项目属性，C++常规，附加包含目录中添加
源码路径\src\core
源码路径\src\gui
源码路径\core\expression
即可

#### 参考资料

https://blog.csdn.net/qq_39506222/article/details/83579888?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_v2~rank_aggregation-3-83579888.pc_agg_rank_aggregation&utm_term=c%2B%2B%E6%8F%92%E4%BB%B6%E5%BC%80%E5%8F%91+qgis&spm=1000.2123.3001.4430