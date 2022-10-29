---
title: QGIS二次开发-配置python环境
date: 2020-12-07 14:42:14
tags: QGIS
categories: GIS
---

使用QGIS PluginBuilder 来创建Python模板的qgis 插件

<!--more-->

## Plugin Builder 创建插件

创建完成后，如果提示没有pyrcc5，

```
pyuic5 -o WYFirstPlugin_dialog.py WYFirstPlugin_dialog_base.ui
pyrcc5 -o resources.py resources.qrc
```

#### 使用QGIS控制台脚本 python查看application

查看安装路径

QgsApplication.prefixPath()

## 配置python插件调试环境

1. 在 Pycharm **Professional** 中打开插件工程文件 （Community 版的不支持远程调试）

2. File -> Project -> Python Interpreter，点击小齿轮按钮，选择 Add

3. 选择 Virtualenv Environment，在 `Base interpreter` 中选择一个本机上已经安装的 Python **3** 解释器；勾选 `Inherit global site-packages` 和 `Make available to all projects`

4. 创建好这个解释器环境后，回到 Python Interpreter 界面，点击小齿轮按钮，选择 Show All，选择下面一排按钮中最右边那个；点击下面那个加号，添加路径 `QGIS安装路径\apps\qgis-ltr\python`

   ![image-20210315201250302](qgis-develop-5-python-env/image-20210315201250302.png)

5. Run -> Edit Configuration...，点击加号，选择 Python Debug Server，设置 `IDE host name` 为 `localhost`, `Port` 任意，比如 53100

6. 在需要调试的模块中添加以下代码，然后选择 Debug 

```python
    import sys
    sys.path.append("PyCharm安装路径/debug-eggs/pydevd-pycharm.egg")
    import pydevd_pycharm
    # 这里的前两个参数要和上一步设置的对应好
    pydevd_pycharm.settrace('localhost', port=53100, stdoutToServer=True,
                            stderrToServer=True)
```

5. 进入文件夹 `PyCharm安装路径/debug-eggs`，将 `pydevd-pycharm.egg` 解压到一个文件夹中，比如 `pydevd-pycharm`

   可以通过修改后缀为rar来解压

6. 在 QGIS 中安装插件 Remote Debug

7. 运行 Remote Debug 插件，`Debug` 选择 `PyDev`，`pydev path` 选择上一步中解压得到的文件夹

8. 运行插件，开始调试 

