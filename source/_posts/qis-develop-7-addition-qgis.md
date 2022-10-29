---
title: QGIS改造源代码
date: 2021-03-02 22:24:51
tags: QGIS
categories: QGIS
---

<!--more-->

1、利用uic.exe 手动生成ui头文件

配置过环境变量QTDIR之后，可以在命令行中使用uic命令

uic "src\ui\qgisapp.ui" -o ".\ms-windows\osgeo4w\build-qgis-test-x86_64\src\ui\ui_qgisapp.h"

2.利用moc.exe 编译出cpp文件

**moc.exe" "自定义的.h" -o "moc_xxx.cpp"**

**每次改动界面，需要全部重新编译工程**

## 几何生成器

```
-- render the centroid of a feature
centroid( $geometry )

-- visually overlap features within a 100 map units distance from a point
-- feature, i.e generate a 100m buffer around the point
buffer( $geometry, 100 )

-- Given polygon layer1( id1, layer2_id, ...) and layer2( id2, fieldn...)
-- render layer1 with a line joining centroids of both where layer2_id = id2
make_line( centroid( $geometry ),
           centroid( geometry( get_feature( 'layer2', 'id2', attribute(
               $currentfeature, 'layer2_id') ) )
         )

-- Create a nice radial effect of points surrounding the central feature
-- point when used as a MultiPoint geometry generator
collect_geometries(
  array_foreach(
    generate_series( 0, 330, 30 ),
      project( $geometry, .2, radians( @element ) )
  )
)
```