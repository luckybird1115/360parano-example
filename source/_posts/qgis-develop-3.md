---
title: QGIS二次开发学习（二）-测试demo
date: 2020-11-18 21:37:21
tags: QGIS
categories: GIS
---

收集的QGIS Demo代码

<!--more-->

## 测试Demo

### Demo1

```c++
#include <qgis.h>
#include "MainWindow.h"
#include <QApplication>
#include <qgis_core.h>
#include <qgis_gui.h>
#include <qgsdatasourceuri.h>
#include <qgsproviderregistry.h>
#include <QDebug>
#include <QSqlDatabase>
#include <QSqlError>
#include <QSqlQuery>
#include <qgssinglesymbolrenderer.h>
#include <qgsapplication.h>
 
#define pluginsPath "F:\\OSGeo4W64\\apps\\qgis-dev\\plugins"
 
//using namespace QgsGuiUtils;
 
int main(int argc, char* argv[]) 
{
	QgsApplication myApp(argc, argv, true);
	myApp.setPrefixPath("F:\\OSGeo4W64\\apps\\qgis",true);
	QgsProviderRegistry::instance("F:\\OSGeo4W64\\apps\\qgis\\plugins");	
	QString file("E:\\Beijing.osm.shp\\Beijing-shp\\shape\\roads.shp");
	
	QList<QgsMapLayer*> listVecLayer;
 
	QgsVectorLayer Vectorlayer;
	Vectorlayer.setProviderEncoding("GBK2312");
	Vectorlayer.setMinimumScale(1.0);
	Vectorlayer.setDataSource(file,QFileInfo(file).completeBaseName(),"ogr",true);
	
	//QgsFeature feature();
	//QgsFeatureList FeatureList;
	//FeatureList.append();
	//QgsVectorLayer usetLayer;
	//usetLayer.addFeatures();
 
 
	listVecLayer.append(&Vectorlayer);
 
	qDebug()<<Vectorlayer.featureCount();
	QgsProject::instance()->addMapLayers(listVecLayer);
 
	QgsMapCanvas QGSMC;
	QGSMC.setLayers(listVecLayer);
	QGSMC.setExtent(Vectorlayer.extent());
	QGSMC.enableAntiAliasing(true);
	QGSMC.freeze(false);
	QGSMC.setDragMode(QGraphicsView::RubberBandDrag);
	QGSMC.setVisible(true);
	QGSMC.zoomToFullExtent();
	QGSMC.refresh();
	QGSMC.show();
	//QgsMapLayer* testlayer = new QgsMapLayer();
	testlayer.show();
 
	//QgsDataSourceUri GS_DATASURL;
	// setConnection（const QString＆aHost，const QString＆aPort，const QString＆aDatabase，const QString＆aUsername，const QString＆aPassword，SslMode sslmode = SslPrefer，const QString＆authConfigId = QString（））
	//GS_DATASURL.setConnection("192.168.2.205","3306", "beijin_map", "root", "root", QgsDataSourceUri::SslMode::SslDisable);
	//GS_DATASURL.setDriver("Mysql");
	//qDebug() << "exit" << GS_DATASURL.table();
 
	QSqlDatabase db = QSqlDatabase::addDatabase("QMYSQL");
	db.setHostName("192.168.2.205");
	db.setDatabaseName("beijing_map");
	db.setUserName("root");
	db.setPassword("root");
	if (db.open()) 
	{
		QSqlQuery qsql = QSqlQuery(db);
		if(qsql.exec("select COLUMN_NAME from information_schema.COLUMNS where table_name = 'testmap'"))
		{
			while (qsql.next()) {
				qDebug() << qsql.value(0).toString();
			}
		}
	}
	else 
		qDebug() << db.lastError();
 
	//MainWindow MW;
	//MW.show();
 
	//QgsMapLayoutConfigWidget QMLCW;
	//QMLCW.show();
 
	return myApp.exec();
}
```

### Demo2

```
//
// QGIS Includes
//
#include <qgsapplication.h>
#include <qgsproviderregistry.h>
#include <qgssinglesymbolrenderer.h>
#include <qgsmaplayerregistry.h>
#include <qgsvectorlayer.h>
#include <qgsmapcanvas.h>
//
// Qt Includes
//
#include <QString>
#include <QApplication>
#include <QWidget>
```

我们用QgsApplication代替Qt中的QApplication来创建应用程序，这样可以方便地调用QGIS中的许多静态方法，比如得到库文件所在的路径等等。

各种数据的提供商plugins通过提供商注册表(QgsProviderRegistry)来管理，它会根据提供商自动加载相应的plugins，我们所需要做的就是指定plugins所存放的目录，让应用程序实例化这样的提供商注册表。

QgsSingleSymbolRenderer是最基本的符号类，它用一种颜色表示点、线、多边形对象。默认情况下颜色是随机选取的，当然，你也可以自己设定希望显示的颜色。每一个矢量图层都有一个对应的符号。

地图图层注册表（QgsMapLayerRegistry）用来管理现在正在使用的图层。QgsVectorLayer 继承自QgsMapLayer，添加了矢量数据特有的功能。

最后，地图画布（QgsMapCanvas）才是问题的关键，我们的地图将被绘制在这个工具上。

现在，我们开始初始化我们的应用程序：

```
int main(int argc, char ** argv)
{
  // Start the Application
  QgsApplication app(argc, argv, true);

  QString myPluginsDir        = "/home/timlinux/apps/lib/qgis";
  QString myLayerPath         = "/home/timlinux/gisdata/brazil/BR_Cidades/";
  QString myLayerBaseName     = "Brasil_Cap";
  QString myProviderName      = "ogr";
```

这样我们就得到了一个qgsapplication。

变量myLayerPath指定了plugins所在的目录。

变量myLayerPath和myLayerBaseName指向要使用的shape文件。

变量myProviderName很重要，它告诉qgis加载数据文件需要使用哪个数据提供商。一般为”ogr”或”postgres”。

现在我们可以开始创建图层了：

```
1 // Instantiate Provider Registry
2   QgsProviderRegistry::instance(myPluginsDir);
```

首先我们需要初始化提供商注册表。QgsMapLayerRegistry是一个单类（singleton class），因此我们使用它的静态函数instance()传递供应商插件库的路径，在此之后它将在这个路径下查找提供商的插件。

接下来我们继续创建图层：

```
QgsVectorLayer * mypLayer =
      new QgsVectorLayer(myLayerPath, myLayerBaseName, myProviderName);
  QgsSingleSymbolRenderer *mypRenderer = new QgsSingleSymbolRenderer(mypLayer->geometryType());
  QList <QgsMapCanvasLayer> myLayerSet;

  mypLayer->setRenderer(mypRenderer);
  if (mypLayer->isValid())
  {
    qDebug("Layer is valid");
  }
  else
  {
    qDebug("Layer is NOT valid");
  }

  // Add the Vector Layer to the Layer Registry
  QgsMapLayerRegistry::instance()->addMapLayer(mypLayer, TRUE);
  // Add the Layer to the Layer Set
  myLayerSet.append(QgsMapCanvasLayer(mypLayer, TRUE));
```

```
// Create the Map Canvas
  QgsMapCanvas * mypMapCanvas = new QgsMapCanvas(0, 0);
  mypMapCanvas->setExtent(mypLayer->extent());
  mypMapCanvas->enableAntiAliasing(true);
  mypMapCanvas->setCanvasColor(QColor(255, 255, 255));
  mypMapCanvas->freeze(false);
  // Set the Map Canvas Layer Set
  mypMapCanvas->setLayerSet(myLayerSet);
  mypMapCanvas->setVisible(true);
  mypMapCanvas->refresh();
  return app.exec();
```

### Demo3

 首先，main.cpp

```
#include "gisTest2.h"
#include <qgsapplication.h>

int main(int argc, char *argv[])
{

   QgsApplication a(argc, argv, true);
   QgsApplication::setPrefixPath("C:/OSGeo4W/apps/qgis", true);
   QgsApplication::initQgis();   //初始化QGIS应用
   gisTest2 w;   //创建一个窗体，类似于Qt
   w.show();

   return a.exec();
}
```

 

gisTest2.cpp代码如下：

```
#include "gisTest2.h"
#include <qmenubar.h>
#include <qmessagebox.h>
#include <qfiledialog.h>
#include <qgsvectorlayer.h>

gisTest2::gisTest2(QWidget *parent)
   : QMainWindow(parent)
{
   this->resize(600, 400);

   // create the menus and then add the actions to them.
   fileMenu = this->menuBar()->addMenu("File");
   openFileAction = new QAction("Open", this);
   this->connect(openFileAction, SIGNAL(triggered(bool)), this, SLOT(on_openFileAction_triggered()));
   fileMenu->addAction(openFileAction);

   // initialize the map canvas
   mapCanvas = new QgsMapCanvas();
   this->setCentralWidget(mapCanvas);

   mapCanvas->setCanvasColor(QColor(255, 255, 255));
   mapCanvas->setVisible(true);
   mapCanvas->enableAntiAliasing(true);

}

void gisTest2::on_openFileAction_triggered() {
   addVectorLayer();
}

void gisTest2::addVectorLayer()
{
   QString fileName = QFileDialog::getOpenFileName(this, tr("Open shape file"), "", "*.shp");
   QStringList temp = fileName.split('/');
   QString basename = temp.at(temp.size() - 1);
   QgsVectorLayer* vecLayer = new QgsVectorLayer(fileName, basename, "ogr");

   if (!vecLayer->isValid())
   {
     QMessageBox::critical(this, "error", QString("layer is invalid: \n") + fileName);
     return;
   }
   mapCanvas->setExtent(vecLayer->extent());
   layers.append(vecLayer);
   mapCanvas->setLayers(layers);
   mapCanvas->refresh();
}
```

gisTest2.h代码

```
#pragma once

#include <QtWidgets/QMainWindow>
#include "ui_gisTest2.h"
#include <qmenu.h>
#include <qaction.h>
#include <qgsmapcanvas.h>


class gisTest2 : public QMainWindow
{
   Q_OBJECT

public:
   gisTest2(QWidget *parent = Q_NULLPTR);

private:
   // create the menus and then add the actions to them.
   QMenu *fileMenu;
   QAction *openFileAction;

   //map canvas
   QgsMapCanvas *mapCanvas;
   QList<QgsMapLayer *> layers;

   public slots:
   void on_openFileAction_triggered();
   //

public:
   void addVectorLayer();

};
```

 

#### 参考资料

https://www.cnblogs.com/loongfee/archive/2012/05/25/2518483.html