---

title: QGIS编译可能遇到的问题
date: 2020-12-04 19:21:13
tags: QGIS
categories: GIS
---



QGIS编译源码可能遇到的问题

<!--more-->

[TOC]



### 1. 修改不识别的符号

先在资源管理器中找到qgis项目，右击该项目，选择“设为启动项目”
![](qgis-development-problem\1)
接着，你可以先试着第一次生成解决方案，以排除所有页面不识别的错误

你应会遇到大量报错，请等待生成完毕。
在错误中会出现例如
“错误 C2143 语法错误: 缺少“)”(在“;”的前面) ”
“错误 C2001 常量中有换行符 ”
……
这些错误的原因是因为页面不支持("′")("″")的符号

在错误列表关闭错误，仅查看警告，并点击“说明”让其聚类。大体上将出现会三种警告类型。
对于以下这两种，我们忽视掉——
“警告 C4718 “QMapNode<int,QgsRasterIterator::RasterPartInfo>::doDestroySubTree”: 递归调用无副作用，正在删除 ”
“警告 C4702 无法访问的代码 ”
![](qgis-development-problem\2)
![](qgis-development-problem\24.png)

而对于——
“警告 C4819 该文件包含不能在当前代码页(936)中表示的字符。请将该文件保存为 Unicode 格式以防止数据丢失 ”
![](qgis-development-problem\3)
我们点双击其文件一栏的文件名，进入对应的.cpp或.h文件
![](qgis-development-problem\25.png)
Ctrl+A 全选文件内容，点击上方“文件”，找到“高级保存设置”
![](qgis-development-problem\4.png)
将其从无签名改为带签名
![](qgis-development-problem\5.png)
修改后——
![](https://www.pianshen.com/images/329/6e63813b4370e61f705143eae8928629.png)
修改所有出现此类警告的.cpp和.h文件，再次生成“qgis”项目，大部分报错将会消失。（也有可能直接编译通过）

### 2. 可能报错的其他问题

#### **2.1与qtmain.lib有关的未初始化的定义**

解决方法：
找到例如此的警告信息
LNK4099 未找到 PDB“qtmain.pdb”(使用“qtmain.lib(qtmain_win.obj)正在链接对象，如同没有调试信息一样
得到该警告来源的项目名，如“qgiscrashhandle”

右击“qgiscrashhandle项目”->“属性”->“连接器”->“输入”附加依赖项中修改添加
D:\QGIS\OSGeo4W64\apps\Qt5\lib\qtmain.lib ![](qgis-development-problem\7.png)

#### **2.2 qgis_gui项目中出现未定义的标识符 "QWebElement"**

解决方法：
“qgis_gui项目”->“属性”->“C/C++”->“常规”附加包含目录中修改添加
D:\QGIS\OSGeo4W64\apps\Qt5\include\QtWebKit
![](qgis-development-problem\8.png)
注释掉报错函数关于QWebElemen类的内容，并在qgismaptip.cpp文件开头注释掉——
//#if WITH_QTWEBKIT
//#endif
（因为原代码中WITH_QTWEBKIT未定义，头文件 不可被识别；又因WITH_QTWEBKIT未定义，QWebElemen类中部分函数无法被调用，故也注释掉）
![](qgis-development-problem\27.png)
![](qgis-development-problem\10.png)

#### **2.3 qgis_gui项目中出现CORE_EXPORT显式实例化声明无效**

解决方法：
对于报错的位置——qgsoptionalexpression.h文件中，在template后添加class
![](qgis-development-problem\11.png)
重新生成解决方案。
如果完整的下载了链接库并正确引入路径，此时，不会再遇到其他问题。
![](https://www.pianshen.com/images/887/43a30ea24c790127b5d8b1be2050184f.png)
我们可以在输出路径下发现生成的qgis.exe文件
（路径：D:\QGIS\qgis-3.2.2-build\output\bin\RelWithDebInfo）

#### 2.4 C1083 keychain.h无法打开包含文件

检查qgis_core中链接器，输入中添加附加包含目录Qt5Keychain.lib

### 3. 其他可能的问题（来源于网络统计）

如果出现问题，优先选择从gis_core项目、qgis_analysis项目、qgis_gui项目依次调试（其他项目均依赖于他们）
此时仍然有可能出现：

#### **3.1 error MSB6006: “rc.exe”已退出，代码为 5**

在C盘下直接搜索该应用，将其路径配置到项目中即可。

#### **3.2 MSVCRT.lib(exe_winmain.obj) : error LNK2019: 无法解析的外部符号 WinMain**

这是因为——
新建项目是控制台应用程序，而程序通过的是WinMian（及windows入口函数）
可以在“qgis_core项目”->“属性”->“连接器”->“输入”附加依赖项中修改添加
D:\QGIS\OSGeo4W64\apps\Qt5\lib\qtmain.lib
![](qgis-development-problem\13.png)
重新生成即可。

#### **3.4 链接错误或者缺少有关附加库问题**

通常表现为——无法解析的外部符号；找不到、打不开.lib文件等
这与个人的主机环境有关，可以在“项目”->“属性”->“连接器”->“输入”附加依赖项中修改。
附上我的部分属性情况

Lib问题，检查附加依赖项
可以在“项目”->“属性”->“连接器”->“输入”附加依赖项中查看修改；

对于Release或者RelWithDebInfo版本，Lib路径基本如下图列举
对于Debug版本，部分Lib库名后有d标识
如下图的Qt5Cored即为Debug版本
（Debug版本与Release（RelWithDebInfo）版本的Lib名称不同，但路径一致。在工程修改版本环境时会自动更新链接，更改库名。无需手动更改。）

#### **3.5 缺少可执行文件或者环境路径问题**

Dll问题，检查可执行文件目录，即检查环境路径
可以在“项目”->“属性”->“VC++目录”->“可执行文件目录”中编辑查看
主要检查有无以下路径：
D:\QGIS\OSGeo4W64\apps\qt5\bin
D:\QGIS\OSGeo4W64\apps\Python37
D:\QGIS\OSGeo4W64\apps\Python37\Scripts
D:\QGIS\OSGeo4W64\bin
D:\Program Files\CMake\bin
D:\QGIS\cygwin64\bin
C:\WINDOWS\system32
C:\WINDOWS
C:\WINDOWS\system32\WBem

------

------

#### 3.6 error MSB6006 cmd.exe已退出，代码为1

查看输出，主要是在构建qtpython中报错

#### 3.7 error MSB23073 

编译过程中找不到一些模块

```
严重性	代码	说明	项目	文件	行	禁止显示状态
错误	MSB3073	命令“setlocal
C:\cmake-3.19.0-win64-x64\bin\cmake.exe -P J:/QGISDEV/QGISBuild1/python/plugins/db_manager/stageinstall.cmake
if %errorlevel% neq 0 goto :cmEnd
:cmEnd
endlocal & call :cmErrorLevel %errorlevel% & goto :cmDone
:cmErrorLevel
exit /b %1
:cmDone
if %errorlevel% neq 0 goto :VCEnd
:VCEnd”已退出，代码为 1。	zzz-db_manager-stageinstall	C:\Program Files (x86)\MSBuild\Microsoft.Cpp\v4.0\V140\Microsoft.CppCommon.targets	133	
```

 QQmlComponent: Component is not ready
79>  file:///J:/QGISDEV/QGISBuild1/src/quickgui/plugin/typelist.qml:2:1: plugin cannot be loaded for module "QgsQuick": Cannot load library J:\QGISDEV\QGISBuild1\src\quickgui\plugin\QgsQuick\qgis_quick_plugin.dll: 找不到指定的模块。

### 3.7 proj相关宏命令重定义

比较坑的地方是OSGeo4W64/include 以及OSGeo4W64/app/proj-dev/include 均有proj.h，需要看你工程里配置的proj库是哪个路径，我的需要修改的是OSGeo4W64/include 下的proj.h设置\#pragma once，注意看include 的引入顺序，默认生成的引用顺序为先定义了proj-dev/include 后定义了OSGeo4W64/include，调整顺序为下图所示：

![image-20201229172653562](qgis-development-problem/image-20201229172653562.png)

**总结：**

**1. 修改OSGeo4W64/include 下的proj.h，在文件开头 添加代码：#pragma once**

**2. 调整c++/常规，附加包含目录，include顺序，先根目录C:\OSGeo4W64\include，再C:\OSGeo4W64\apps\proj-dev\include**

3.步骤2需要设置的工程包括：qgis_core,qgis_gui,qgis_app,qgis_process

### 3.8新建C++插件

### 4. python相关

Debug方式编译生成解决方案时，提示缺少python37_d.lib，需要安装python debug版本，如果没有，可以修改已经安装的python，如下图所示

勾选最后一项，然后install，安装后可以在附加依赖项中添加python37_d.lib

#### 4.1 在python中执行from PyQt5.QtCore import * 提示引入模块找不到

 from qgis._core import *
ImportError: DLL load failed: 找不到指定的程序。

【解决方案】:使用镜像安装sip,pyqt5,pyqt5-tools

` pip install pyqt5 pyqt5-tools -i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com`

#### 4.2 在python中执行from PyQt5.Qsci import * 提示引入模块找不到

【解决方案】:使用镜像安装QScintilla

#### 4.3 Python ImportError：DLL load failed： %1不是有效的win32应用程序

错误本身是因为python版本位数 pywin32版本不一致，网上说是因为需要下载安装对应版本pywin32

我遇到的问题是，在编译qgis的时候，同时通过exe的方式安装了其他版本的qt,在查找使用包的时候使用了另一个qgis版本下的

https://github.com/mhammond/pywin32/releases

可以下载安装试试，我目前还没尝试

#### 4.4 qgis_quick_plugin

#### 4.5 C2248 python_module_qgis_core sip_corepart6.cpp QgsAuthManager 无法访问protected 成员

## 运行成果

------

### 配置运行环境

编译好的qgis.exe会在目录D:\QGIS\qgis-3.2.2-build\output\bin\RelWithDebInfo下

但打开qgis.exe时会出现报错
![](qgis-development-problem\22.png)
此时，把OSGeo4W64\apps\Qt5\bin和OSGeo4W64\bin下的dll文件全部拷贝到exe文件同目录下即可

同时，把OSGeo4W64\apps\Qt5\plugins文件下的platforms文件夹也拷贝到exe文件同目录下
当程序运行时，找不到正确支持图标格式（svg）的库文件。这里需要把OSGeo4W64\apps\Qt5\plugins文件下的imageformats文件夹也拷贝到exe文件同目录下
![](qgis-development-problem\23.png)
再次打开运行qgis.exe