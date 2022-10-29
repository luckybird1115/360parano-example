---
title: QGIS 按照官方指南编译源码
date: 2020-12-22 14:35:20
tags: QGIS
categories: QGIS
---

最终成功编译QGIS的详细步骤，可以不用来cmake-gui界面，就可以配置

<!--more-->

## 软件环境准备

### VS2015需要安装的开发工具

Select "Custom" install and add the following packages:

- "Common Tools for Visual C++ 2015" under "Visual C++"

- "Tools (1.4.1) and Windows 10 SDK (10.0.14393)" under "Universal Windows App Development Tools".

  ![image-20201206195049105](qgis-develop-5\image-20201206195049105.png)

### 下载软件包

| Tool    | Website                                                      |
| ------- | ------------------------------------------------------------ |
| CMake   | https://cmake.org/files/v3.7/cmake-3.7.2-win64-x64.msi       |
| cygwin  | http://cygwin.com/setup-x86.exe (32bit) or http://cygwin.com/setup-x86_64.exe (64bit) |
| OSGeo4W | http://download.osgeo.org/osgeo4w/osgeo4w-setup-x86.exe (32bit) or http://download.osgeo.org/osgeo4w/osgeo4w-setup-x86_64.exe (64bit) |
| ninja   | https://github.com/ninja-build/ninja/releases/download/v1.7.2/ninja-win.zip |

对于cygwin和OSGeo4W，下载完后都是选择高级安装。

在选包界面的搜索栏输入文档中给出的包名，目前官方文档给出的是：

#### 1.cygwin

\- bison
\- flex

![image-20201122191237032](qgis-develop-5\18.png)

**设置环境变量：Path中添加C:\cygwin64\bin**

![image-20201122191332862](qgis-develop-5\19.png)

**检查是否安装成功flex和bison**,见下图

![image-20201122191537468](qgis-develop-5\20.png)

**2.OSGeo4W:**

下载地址：  http://download.osgeo.org/osgeo4w/osgeo4w-setup-x86.exe (32bit) or http://download.osgeo.org/osgeo4w/osgeo4w-setup-x86_64.exe (64bit)

选择Advance Install ,在线下载时，使用镜像http://gwmodel.whu.edu.cn/mirrors/osgeo4w，不然会下载特别缓慢

另外之后编译的过程中如果发现有缺失的包也是可以重新在这里补充下载的。

**搜索qgis-dev-deps**

![img](qgis-develop-5\70.png)

直接下一步完成安装即可。

#### 3.安装CMake

https://cmake.org/download/

![image-20201122184022077](qgis-develop-5\16.png)

#### 4.ninja(https://github.com/ninja-build/ninja/releases/download/v1.7.2/ninja-win.zip)

下载后解压，将ninja.exe 拷贝到

### 配置环境变量

#### 1.path中添加cmake,osgeo4W64等环境变量

C:\OSGeo4W64\bin

D:\cygwin64\bin

D:\cmake-3.19.0-win64-x64\bin



![image-20201225191938744](qgis-develop-5\image-20201225191938744.png)

#### 2.开发python插件所需要的环境变量配置

![image-20201225191859911](qgis-develop-5\image-20201225191859911.png)

## QGIS源码编译

### 1. 克隆或者下载源代码

官方例子放在了OSGeo4W下，

```
 cd C:\OSGeo4W64
 git clone git://github.com/qgis/QGIS.git
```

### 2. 在OSGeo4W64下，创建OSGeo4W-dev.bat文件

首先运行OSGeo4W64下的bat,设置qt等环境变量，然后保存并运行该批处理文件

```
 @echo off
    call C:\OSGeo4W64\QGIS\ms-windows\osgeo4w\msvc-env.bat x86_64
@cmd
```

### 3.使用configonly.bat创建VS工程

**如果遇到提示cmake 非法命令，可以修改msvc-env.bat中path的设置，**

```
cd C:\OSGeo4W64\QGIS\ms-windows\osgeo4w
configonly.bat
```

**如果提示Flex报错等，可以检查msvc-env.bat中的设置默认是c:\cygwin64\bin,如果不是，注意修改路径**

![image-20201225192436078](qgis-develop-5\image-20201225192436078.png)

#### 4.编译QGIS

在运行了**OSGeo4W-dev.bat**文件之后，在创建编译的工程，确保所有的环境变量被正确设置

**运行 call gdal-dev-env.bat 来添加gdal和proj库路径**

**输入 devenv打开VS**

### 4.编译过程报错解决

#### 1.error LNK2019

 error LNK2019: 无法解析的外部符号 "__declspec(dllimport) public: static class QString __cdecl QgsOpenClUtils::deviceInfo(enum QgsOpenClUtils::Info,class cl::Device)" (__imp_?deviceInfo@QgsOpenClUtils@@SA?AVQString@@W4Info@1@VDevice@cl@@@Z)，该符号在函数 

解决方案1：由于项目字符集设置问题，根据提示将指定项目的字符集设置为与DLL一致，具体设置如下：项目属性->配置属性-->常规->项目默认值->字符集中设置字符集（测试没有用）

**最终解决：再解决了问题2中的重定义问题后，问题1也修复**

#### 2.PJ_CONTEXT重定义

比较坑的地方是OSGeo4W64/include 以及OSGeo4W64/app/proj-dev/include 均有proj.h，需要看你工程里配置的proj库是哪个路径，我的需要修改的是OSGeo4W64/include 下的proj.h设置\#pragma once，注意看include 的引入顺序，默认生成的引用顺序为先定义了proj-dev/include 后定义了OSGeo4W64/include，调整顺序为下图所示：

![image-20201229172653562](qgis-develop-5\image-20201229172653562.png)

**总结：**

**1. 修改OSGeo4W64/include 下的proj.h，在文件开头 添加代码：#pragma once**

**2. 调整c++/常规，附加包含目录，include顺序，先根目录C:\OSGeo4W64\include，再C:\OSGeo4W64\apps\proj-dev\include**

3.步骤2需要设置的工程包括：qgis_core,qgis_gui,qgis_app,qgis_process

#### 3.如果编译过程中提示找不到PyQT5的module等错误

运行OSGeo4W-dev.bat，执行call gdal-dev-env.bat,使用devenv命令启动vs2015,再打开工程

#### 成功之后，设置qgis为启动项，运行，就可以看到QGIS界面了

![image-20201229184038694](qgis-develop-5\image-20201229184038694.png)

![image-20201229185257473](qgis-develop-5\image-20201229185257473.png)

#### 参考文档

官方github下的install.md

### 注意事项

如果没有将软件vs、osgeo4w64安装在默认的C盘，需要修改configonly.bat中相关环境变量的地址

