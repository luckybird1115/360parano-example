---
title: QGIS Server初步学习
date: 2021-04-01 15:19:07
tags: QGIS
categories: QGIS
---

<!--more-->

# QGIS Server安装与使用（一）

## WMS

测试获取详情

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetCapabilities&map=D:/xampp/cgi-bin/myproject/styling_road_maps.qgs

测试范围

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&map=D:/xampp/cgi-bin/styling_road_maps.qgs&BBOX=-122.36378383843272388,47.57745201656249634,-122.2956899602121581,47.6309678726616923&WIDTH=500&HEIGHT=500&CRS=EPSG:4326

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&map=D:/xampp/cgi-bin/styling_road_maps.qgs&WIDTH=500&HEIGHT=500&CRS=EPSG:4326

测试图层

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&map=D:/xampp/cgi-bin/myproject/styling_road_maps.qgs&BBOX=-122.36378383843272388,47.57745201656249634,-122.2956899602121581,47.6309678726616923&WIDTH=500&HEIGHT=500&CRS=EPSG:4326&Layers=lines

测试获得图例

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?&SERVICE=WMS&VERSION=1.3.0&REQUEST=GetLegendGraphic&LAYER=lines&FORMAT=image/png&STYLE=default&SLD_VERSION=1.1.0&map=D:/xampp/cgi-bin/myproject/styling_road_maps.qgs

测试中文工程

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&map=G:/VSWorkspace/AIData/qgis/毕设数据/贵州测试数据/TestProject.qgs&WIDTH=500&HEIGHT=500&CRS=EPSG:4057&Layers=主区设色&BBOX=634043.32977723015937954,2963074,676295.17148699390236288,2966794.11649530567228794



http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&map=G:/VSWorkspace/AIData/qgis/%E6%AF%95%E8%AE%BE%E6%95%B0%E6%8D%AE/%E8%B4%B5%E5%B7%9E%E6%B5%8B%E8%AF%95%E6%95%B0%E6%8D%AE/TestProject.qgs&WIDTH=500&HEIGHT=500&CRS=EPSG:4326&BBOX=634043.32977723015937954,2963074,676295.17148699390236288,2966794.11649530567228794

## WMTS

```
http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?VERSION=1.3.0&map=D:/xampp/cgi-bin/myproject/styling_road_maps.qgs&SERVICE=WMTS&REQUEST=GetTile&LAYER=lines&FORMAT=image/png&TILEMATRIXSET=EPSG:4326&TILEROW=0&TILECOL=0
```

测试获得信息

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?SERVICE=WMTS&VERSION=1.3.0&REQUEST=GetCapabilities&map=D:/xampp/cgi-bin/myproject/styling_road_maps.qgs

获取Tile瓦片

http://localhost:8080/cgi-bin/qgis_mapserv.fcgi.exe?VERSION=1.3.0&map=D:/xampp/cgi-bin/myproject/styling_road_maps.qgs&SERVICE=WMTS&REQUEST=GetTile&LAYER=lines&FORMAT=image/png&TILEMATRIXSET=EPSG:4326&TILEROW=961&TILECOL=1303&TileMatrix=12

