---
title: QGIS相关资料整理
date: 2021-03-03 22:05:10
tags: QGIS
categories: QGIS
---

QGIS 资料收集总结

<!--more-->



# 1.qgis自定义符号库

转载:https://blog.csdn.net/wywywywywywy123456/article/details/66971218

地图符号作为地图语言；抽象描述的客观世界并用直观、生动视觉的手段向我们传递地理信息。每款GIS软件都会自带一些常见的地图符号，也可以自定义地图符号。一般做专题地图那些常规符号库就不能满足我们。

在使用qgis时候发现自带地图符号少的可怜而且又不美观。于是我引进第三方地图符号；由于qgis自定义允许svg格式我去mapbox官网下载一套svg格式。svg网上资源还是蛮丰富的；加载qgis里只要设置一下路径可以。

![img](qgis-develop-custom-symbollib/20170327151252041)

右击Properties选择样式类型SVG。

![img](qgis-develop-custom-symbollib/20170327151429451)

当然可以通过lnkscape软件来做符号，然后在保存SVG格式。

![img](qgis-develop-custom-symbollib/20170327152341462)

也可以写XML文件来添加地图符号。
![img](qgis-develop-custom-symbollib/20170327152759185)

![img](qgis-develop-custom-symbollib/20170327153028412)



参考资料

http://docs.qgis.org/2.14/en/docs/training_manual/basic_map/symbology.html

https://www.mapbox.com/maki-icons/
https://thenounproject.com/

https://mapicons.mapsmarker.com/

# 2.QGIS切换在线地图服务的地图样式

转载：https://blog.csdn.net/weixin_29002209/article/details/112558162


底图是栅格瓦片服务，所以我们不能对其内容进行控制，更不能对其标注进行修改。不过这一切，在最新的QGIS中，借助一个插件，就可以实现底图效果的自定义了，不仅可以修改不同图层的样式，还可以直接将不想要的图层、标注直接关掉。

**插件MapTiler**


默认提供了7种地图样式，你可以在这些基础上进行改造。我使用最多的是Basic、Streets和Toner。


需要你输入一个Key才行。点击面板中的地址自己申请一个就可以啦。

申请完毕填上后，数据就可以加载到工程中啦。


此时，我们在图层树上选择所加载的图层，右侧就会出现其对应的设置面板。新添加的底图服务面板中，都是每一个可以设置的图层，可以对其进行开关勾选，更可以对其进行样式设置。然，不仅可以对图层效果进行设置，对图层标注也是可以修改的。

# 3.QGIS自定义符号

[QGIS开发之自定义符号](https://www.cnblogs.com/freeza/p/7799227.html)

在程序里添加了编辑矢量图层符号的功能，右键LayerTree的矢量图层，选择“Edit Symbol”，便可以选择或编辑图层的渲染符号。

![img](qgis-develop-custom-symbollib/1092574-20171107144044106-443485294.png)

实现方法很简单，首先参照http://blog.csdn.net/deirjie/article/details/50428179，实现自定义的右键菜单类，具体代码可参考源码中的**QgsAppLayerTreeViewMenuProvider**类。源码里有一个**editVectorSymbol()**方法，原封不动地copy到自己的代码里就行了。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 void QgsAppLayerTreeViewMenuProvider::editVectorSymbol()
 2 {
 3   QAction* action = qobject_cast< QAction*>( sender() );
 4   if ( !action )
 5     return;
 6 
 7   QString layerId = action->property( "layerId" ).toString();
 8   QgsVectorLayer* layer = dynamic_cast<QgsVectorLayer*>( QgsMapLayerRegistry::instance()->mapLayer( layerId ) );
 9   if ( !layer )
10     return;
11 
12   QgsSingleSymbolRendererV2* singleRenderer = dynamic_cast< QgsSingleSymbolRendererV2* >( layer->rendererV2() );
13   if ( !singleRenderer )
14     return;
15 
16   QScopedPointer< QgsSymbolV2 > symbol( singleRenderer->symbol() ? singleRenderer->symbol()->clone() : nullptr );
17   QgsSymbolV2SelectorDialog dlg( symbol.data(), QgsStyleV2::defaultStyle(), layer, mView->window() );
18   dlg.setMapCanvas( mCanvas );
19   if ( dlg.exec() )
20   {
21     singleRenderer->setSymbol( symbol.take() );
22     layer->triggerRepaint();
23     mView->refreshLayerSymbology( layer->id() );
24   }
25 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

以下为点状的Symbol Selector实现效果：

![img](qgis-develop-custom-symbollib/1092574-20171107145223653-49426581.png)

 

由于QGIS线状符号库里没有箭头符号，中午时使用QGIS桌面版自定义了一个带箭头的线状符号。线型为Marker Line，Marker placement为“on last vertex only”；Marker的类型为SVG marker，使用的箭头svg图标路径是开发包路径下的/svg/arrows/Arrow_05.svg；在设置一下点和线的尺寸、偏移、角度等参数便可以得到了。

![img](qgis-develop-custom-symbollib/1092574-20171107145937638-2055373815.png)

![img](qgis-develop-custom-symbollib/1092574-20171107150717138-2078904463.png)

![img](qgis-develop-custom-symbollib/1092574-20171107150849950-1809473349.png)

 

跟ArcGIS中的箭头符号对比一下：

 ![img](qgis-develop-custom-symbollib/1092574-20171107151000559-1200382917.png)

出乎我意料的是，在运行自己的代码时，发现程序的Symbol Selector跟桌面版的一样，也多了这个新定义的“GPS Arrow”符号。也就是说，这个符号设置应该是配置到了一个QGIS的类似环境变量的地方，经过一番探索发现，用户文件夹下有一个.qgis2的文件夹（我的是C:\Users\FangZhe\.qgis2）里面存放的应该是QGIS的配置数据。文件夹下有一个**symbology-ng-style.db**二进制文件。测试了一下，将此文件移走，再次打开符号编辑对话框，里面的符号样式就只剩默认符号了，此时.qgis2文件夹下又生成了一个新的**symbology-ng-style.db**文件。由此看来，此文件确实就是QGIS的符号库文件。日后发布程序时，应该只需要设置将此文件预先添加到配置文件夹之中就可以了，如此便可以省去用代码自定义符号的工作。

C:\Users\Administrator\AppData\Roaming\QGIS\QGIS3\profiles\default

![img](qgis-develop-custom-symbollib/1092574-20171107152250341-1723983819.png)

现在对.qgis2文件夹还是不甚了解，不过暂时不想多花力气研究它，日后用到时再说吧。

感谢博主Jacory_Gao的博客，对我的开发工作帮助巨大。关于如何使用代码自定义符号样式，可参考这篇文章：http://blog.csdn.net/deirjie/article/details/50464768



# 使用第三方SVG符号库导入QGIS

1、下载地址：

mapbox官网github

mapbox\mapbox-gl-styles-master\mapbox-gl-styles-master\sprites

maki:https://labs.mapbox.com/maki-icons/

一种方法是将下载的SVG文件夹路径在以下位置添加其路径：

```
Settings->Options->System->SVG Path
```

这样，您在QGIS样式器中将SVG符号作为组找到（“图层属性”->“样式...”。）

第二种方式：将符号复制到默认文件夹下，通常路径为：

C:\Users\Administrator\AppData\Roaming\QGIS\QGIS3\profiles\default

第三种、使用qgis中共享的SVG

安装插件Resource Sharing ，搜索maki,可以下载mapbox 的maki符号库

# 一款插件SLYR ESRI到QGIS兼容插件

https://north-road.com/slyr/

- 支持将MXD，MXT和PMF文档自动转换为QGIS项目，包括：
  - 支持转换**矢量和栅格图层**，与原始ArcMap外观非常精确地匹配
  - 支持转换层联接和关系
  - 页面布局转换为QGIS打印布局，完全支持使用多个数据框的多地图页面布局
  - 将数据驱动页面转换为QGIS打印图集
  - MXD文档元数据
- 将ESRI **LYR**文件转换为等效的QGIS（包括符号系统！）
- 读取和转换ESRI **.style**数据库
- 在QGIS中**无缝**，集成地处理MXD，LYR和其他ArcMap文档（例如，通过QGIS浏览器面板以及通过拖放操作）
- 一套**功能强大的**处理算法，可将ArcMap文档批量转换为QGIS格式
- 支持**将**图层直接从ArcMap或ArcCatalog**拖放**到QGIS窗口，并遵守所有图层样式。
- 支持**将**ArcMap页面布局元素直接**复制和粘贴**到QGIS打印布局中
- 支持AVL样式转换
- 对ArcMap .dat书签文件的集成支持
- 支持将ArcScene SXD文档转换为二维QGIS地图

SLYR可以将多种ESRI符号类型转换为等效的QGIS符号体系：

- 标记符号：简单标记，箭头标记，字符标记，图片标记，3d标记符号
- 线符号：简单线，制图线，标记线，哈希线和简单3d线
- 填充符号：简单填充，线条填充，标记填充，随机标记填充，图片填充和渐变填充

# 获取空气质量数据在QGIS进行可视化

## 数据收集和预处理

城市点数据来自天地图的1：100万地形数据（2017版），WFS服务地址为：http://gisserver.tianditu.gov.cn/TDTService/wfs

空气质量指数来自中华人民共和国生态环境部官网：https://www.mee.gov.cn/

1. 首先，收集和整理空气质量指数。从生态环境部将所需要的数据下载，整理为三个字段：AQI、空气质量、城市，保存为CSV分隔文本文件。其中AQI为整型字段，用于制作空气质量指数分级专题图；空气质量是对应AQI分级的中文描述；城市名称将作为连接字段与矢量数据挂接。

2.  其次，利用QGIS提取和处理天地图1：100万地形数据（2017版）居民点数据。打开QGIS，在【浏览】面板找到“WFS/OGC API-Features”节点，右键->【新建连接】，在弹出的对话框，名称填写“tianditu”，URL输入：“http://gisserver.tianditu.gov.cn/TDTService/wfs”，点击【OK】完成WFS连接。

3. 展开【浏览器】面板->【WFS/OGC API-Features】->【tianditu】，可以看到该数据服务下共有AANP、AGNP、BOUA、BOUL等十个图层，对应居民点、行政区划界线、水系、道路等要素，其中AGNP为带名称的居民点，将其拖拽到地图窗口，同时打开对应的属性表，观察数据情况：可以看到，AGNP图层共包含OBJECTID、CLASS、NAME、PINYIN、GNID、XZNAME六个字段，其中CLASS表示居民点分类，通过分析可知，当CLASS取值为“AD”时，可提取到城市点。

4. 点击属性表窗口左下角的下拉菜单，选择【高级过滤（表达式）】，打开表达式字符串过滤器，输入下列表达式筛选出城市点："CLASS"  =  'AD'点击【OK】完成表达式筛选，返回属性表窗口，共过滤出415个要素，点击表格左上角，选中所有过滤结果：切换到QGIS地图主窗口，右键点击【图层】面板->【AGNP】->【导出】->【另存选中的要素为…】，弹出【矢量图层另存为】对话框，设置如下参数：格式：GeoPackage；文件名：任意存储路径\city.gpkg；其他参数保持默认，点击【OK】将筛选到的城市点保存为单独的图层。

5. 最后，建立空气质量指数与城市点数据的连接。在【浏览器】面板找到上一步骤保存的城市点图层和空气质量指数，拖拽到地图窗口。右键点击城市点图层->【属性】->【连接】： 击左下角的 按钮，打开新建连接对话框，设置如下参数：

   ​	连接图层：AQI20210128；

   ​	连接字段：城市；

   ​	目标字段：NAME。

   ![img](qgis-develop-custom-symbollib/a1dd5c2703fa6e4f6a13c9c7a2860a5e.png)

   点击【OK】完成连接设置。

6.  关闭图层属性对话框，返回QGIS主窗口，打开城市点图层的属性表窗口，可以看到属性字段增加了AQI、空气质量和城市，说明AQI数据已经连接到城市点图层，至此，数据预处理工作完成，开始空气质量指数地图可视化设置。

##  2、空气质量指数分级


根据我国生态环境部科技标准司于2012年发布的《环境空气质量指数（AQI）技术规定（试行）（HJ 633—2012）》，我国的空气质量指数可以分为六个等级，具体分级标准如下：

 ![img](qgis-develop-custom-symbollib/fbd61c00e048d602e60d433820779379.png)

使用QGIS的“渐进”渲染，可以将上述标准表达在地图上。 

选中城市点图层，点击【图层】面板上方的 按钮，打开【图层样式】面板，点击渲染器下拉框，选择“渐进”渲染：设置“渐进”渲染的参数如下： 

值：AQI20210128_AQI；

图例格式：精度-2，勾选“裁剪”；

模式：等数量（分位数）；

类：6。

点击【分类】按钮，将城市的AQI分为6个等级，如下图：默认的分级断点和样式与我国的标准并不一致，需要手工调整。首先，修改一级AQI的符号颜色为标准规定的绿色，RGB值为（72, 228, 0）：双击分类第一行中的【符号】， 在符号图层选中【标记】->【简单标记】->【填充颜色】->【选择颜色】，分别填入颜色的R、G、B分量：72, 228, 0。

 第二步，修改分级的上下限。双击第一行的【值】，弹出上下限设置对话框，输入【下界值】为0；【上界值】为50：

 最后，修改图例的文字，双击第一行的【图例】，进入编辑状态，输入文字“优”：

 按同样步骤完成其他AQI级别的符号、上下限、图例的修改，完成后效果如下图：

## 3.添加文字标注


在【图层样式】面板，切换到 标注标签页，选择“单一标注”，点击【值】右侧的 按钮，打开【表达式对话框】，输入如下表达式作为标注的内容：

"NAME"  ||  '\n'  || "AQI20210128_AQI"


接下来设置标签的背景颜色。切换到 背景，勾选“绘制背景”，设置【大小 横坐标】为2，【纵向大小】为0.5，点击【填充颜色】右侧的 按钮，打开【表达式字符串构造器】对话框，输入如下表达式设置标注的背景色：

@symbol_color


此时可以看到，有些标注压盖了城市点，需要设置标注的位置以解决压盖问题。切换到 位置，将【模式】设置为“离点偏移量”，【横、纵坐标偏移】分别输入0.0000，-6.0000。

为了明确标注与城市的对应关系，可以绘制引出线，即标注牵引线。切换到 引出线，勾选“绘制引出线”，将【从要素偏移】设置为1毫米。

最后，为地图选择一个比较素净的网络地图作为背景图，本文选择使用OpenStreetMap monochrome作为底图，其XYZ连接URL为：http://a.tiles.wmflabs.org/bw-mapnik/{z}/{x}/{y}.png。也可以使用国内的天地图、高德地图等（关于如何加载网络地图，请参考：浅谈XYZTiles 和 通过QGIS XYZ Tiles访问国内四大图商地图服务）。最终效果如下：



转载原文链接：https://blog.csdn.net/QGISClass/article/details/113360573