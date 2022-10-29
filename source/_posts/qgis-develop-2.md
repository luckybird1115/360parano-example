---
title: QGIS源码编译
date: 2020-11-18 21:14:18
tags: QGIS
categories: GIS
---

QGIS 源码编译，为学习开源软件以及二次开发做准备

资料收集于网络资源，后经测试，通过官方自带的批处理能够方便的编译出qgis工程

<!--more-->

使用老的方式，通过cmake-gui配置环境变量编译qgis 源码，很麻烦，会有各种各样的错

### VS2015需要安装的开发工具

Select "Custom" install and add the following packages:

- "Common Tools for Visual C++ 2015" under "Visual C++"

- "Tools (1.4.1) and Windows 10 SDK (10.0.14393)" under "Universal Windows App Development Tools".

  ![image-20201206195049105](qgis-develop-2\image-20201206195049105.png)

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

![image-20201122191237032](qgis-develop-2\18)

**设置环境变量：Path中添加C:\cygwin64\bin**

![image-20201122191332862](qgis-develop-2\19)

**检查是否安装成功flex和bison**,见下图

![image-20201122191537468](qgis-develop-2\20)

**2.OSGeo4W:**

下载地址：  http://download.osgeo.org/osgeo4w/osgeo4w-setup-x86.exe (32bit) or http://download.osgeo.org/osgeo4w/osgeo4w-setup-x86_64.exe (64bit)

选择Advance Install ,在线下载时，使用镜像http://gwmodel.whu.edu.cn/mirrors/osgeo4w，不然会下载特别缓慢

另外之后编译的过程中如果发现有缺失的包也是可以重新在这里补充下载的。

![img](qgis-develop-2\70)

直接下一步完成安装即可。

注:如果不需要编译源码，只需要下载qgis相关即可，后续如果还需要编译源码，需要下载一些编译依赖库：

Qt >= 4.5.0（QT5随OSGeo4W自动安装，在OSGeo4W目录下apps中）

Proj >= 4.4.x（随OSGeo4W自动安装，在OSGeo4W目录下apps中）

GEOS >= 3.0，

Sqlite3 >= 3.0.0(暂没用到)

GDAL/OGR >= 1.4.x（随OSGeo4W自动安装，在OSGeo4W目录下apps中）

Qwt >= 5.0 & (< 6.1 with internalQwtPolar)(随OSGeo4W自动安装，在OSGeo4W目录下)

expat >= 1.95(随OSGeo4W自动安装，在OSGeo4W目录下)

\- QScintilla2
\- QCA
\- qtkeychain (>= 0.5)

可选依赖库：
\- GRASS驱动和插件支持 - GRASS >= 6.0.0. QGIS可以和GRASS6或者GRASS7共同编译. 它也可以同时支持这两个版本的GRASS如果安装了rpath的话，具体使用的版本是由LD_LIBRARY_PATH或者PATH变量在运行时决定。
\- georeferencer - GSL >= 1.8
\- postgis数据库支持 - PostgreSQL >= 8.0.x
\- gps插件 - gpsbabel
\- mapserver导出以及PyQGIS - Python >= 3.3
\- python支持 - SIP >= 4.12, PyQt >= 5.3 并且要与Qt版本吻合, Qscintilla2
\- qgis mapserver - FastCGI
\- oracle驱动 - Oracle OCI library

安装完cygwin和OSGeo4W后，**将ninja.exe复制到之前安装OSGeo4W目录的OSGeo4W64\bin\下**。、

![img](qgis-develop-2\22)

#### 安装CMake

https://cmake.org/download/

![image-20201122184022077](qgis-develop-2\16)

**新建CMAKE_ROOT的环境变量**

打开CMakeGUI程序，选择QGIS源码（https://github.com/qgis/QGIS），以及存放编译生成的工程的文件夹路径

### 编译源码

官方提供了两种方法，使用Trugonly.cmd创建MSVC解决方案文件或者使用cmake-gui

由于各种路径设置的原因，笔者建议还是使用传统的cmake进行编译。

##### **1.运行 C:\OSGeo4W64\bin下的o4w_env.bat文件，安装好环境变量**

不难发现，其中涉及到的重要的环境变量主要有：

OSGEO4W_ROOT（OSGeo4W的根目录）

PF86（软件默认安装目录）

VS140COMNTOOLS（调用VS的vcvarsall.bat批处理文件）

GRASS7（这个的路径中是【/】而不是【\】，要注意）

PYTHONPATH（SIP包所在路径）

LIB（OSGeo4W依赖库头文件）

INCLUDE（OSGeo4W静态库文件）

如果之前更改了路径，修改相应的环境变量即可。

其中，VS140COMNTOOLS是VS2015的变量名，

##### 打开cmake-gui，设置好源码路径和要输出的路径，然后点击Configure

![image-20201122185955306](qgis-develop-2\17)

另外在CMAKE卡中设置项目要安装的路径，推荐设置在一个新的空目录中，避免导致混乱

![img](qgis-develop-2\3)

出现错误也是正常的，关键还是路径的问题，所以说前面环境变量的设置十分重要

##### 配置环境变量

一步一步的指定缺失的路径，首先是flex和bison**，这一步，主要在安装cygwin并下载flex和bision后，将cygwin/bin路径添加到Path中即可**

![img](qgis-develop-2\6)

![img](https://img-blog.csdn.net/20180801151521772?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MzU3NzE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后是各个库

![image-20201122192309206](qgis-develop-2\21)

![img](qgis-develop-2\7)

这个基本在安装OSGeo4W64时已经全部安装过了，设置路径即可

![image-20201122205035392](qgis-develop-2\24)

![image-20201122211026744](qgis-develop-2\25)

设置完成后继续点Configure，有错误则设置好需要的路径直到出现

![img](qgis-develop-2\11)

然后点击Generate，其实这三个按钮点依次点过去，没问题的话就OK了，成功的话可以在指定的文件夹中看到编译成功的项目，用VS打开项目并且重新生成就可以了，当然这可能需要比较长的一段时间。

 

![img](qgis-develop-2\12)![img](qgis-develop-2\13)

将活动解决方案设置为RelWithDebInfo，带有调试信息的Release版本。

![img](qgis-develop-2\14)

 

将启动项目设置成【qgis】，选择核心的项目生成即可，这里我参考了https://blog.csdn.net/quinta_2018_01_09/article/details/79084001这篇博客。但通过查看看依赖项我发现【qgis】还需要依赖【qgis_native】这个项目，因此也把它加上去了。

![img](qgis-develop-2\15)

![img](https://img-blog.csdnimg.cn/2018122716553618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MzU3NzE3,size_16,color_FFFFFF,t_70)

然后单独编译生成【qgis_core】,如果出现以下问题，定位到出问题的cpp文件，利用记事本对其进行编辑（其他方法亦可），将其编码改为【Unicode】，虽然错误看起来很多，但是实际上几条错误都是在同一个文件中的，实际上需要修改的文件并不多，逐一修改即可。成功生成【qgis_core】后，生成其它项目，出现语法错误处理方式同上，最后生成【qgis】项目。

![img](https://img-blog.csdnimg.cn/20181227175610298.png)

![img](https://img-blog.csdnimg.cn/20181227175756127.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MzU3NzE3,size_16,color_FFFFFF,t_70)

### 其他问题

尤其是确保**不要**安装msinttypes软件包，它会安装stdint.h文件到OSGeo4W[64]/include目录下，进而导致与Visual Studio本身的stdint.h文件冲突，致使编译某些模块失败。

编译的时候，遇到缺少一些python包，需要通过pip安装一下

遇到安装了PyQT5