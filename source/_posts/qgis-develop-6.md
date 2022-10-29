---
title: QGIS插件开发学习
date: 2020-12-26 23:09:17
tags: QGIS
categories:
---

python PyQGIS插件开发代码学习片段

<!--more-->

## Python插件开发部分代码片段

#### QGIS设置支持mdb

点击设置》选项》系统，在环境，勾选使用自定义变量，添加两个环境：

OGR_SKIP ODBC，

PGEO_DRIVER_TEMPLATE DRIVER=Microsoft Access Driver (*.mdb, *.accdb);DBQ=%s

![image-20201226231641904](qgis-develop-6\image-20201226231641904.png)



## QGIS自定义Python表达式

参考资料：https://gis.stackexchange.com/questions/386223/qgis-custom-python-expressions-not-loading-on-startup

### XML相关

https://doc.qt.io/qtforpython-5/PySide2/QtXml/QDomNode.html