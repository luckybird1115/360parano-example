---
title: QGIS二次开发学习（一）
date: 2020-11-18 20:59:11
tags: QGIS
categories: GIS
---

<!--more-->

# QGIS基本资料

QGIS git  https://github.com/qgis

QGIS 快速指南 

用户手册 https://docs.qgis.org/3.16/en/docs/user_manual/index.html

教程 https://docs.qgis.org/3.16/en/docs/training_manual/index.html

QGIS Python 开发手册

https://docs.qgis.org/3.16/en/docs/pyqgis_developer_cookbook/index.html

 PyQGIS API https://qgis.org/pyqgis/

QGIS C++ 开发手册 https://qgis.org/api/

QGIS API https://qgis.org/api/3.14/api_break.html

早期3.1.0.0的用户手册https://docs.huihoo.com/qgis/qgis-1.0.0-user-guide-en.pdf

开发人员手册：https://docs.qgis.org/3.16/en/docs/developers_guide/index.html

QgsDiagramRendererV2

几何生成器函数介绍：https://docs.qgis.org/3.16/en/docs/user_manual/working_with_vector/functions_list.html?highlight=tapered_buffer#geometry-functions

插件目录：http://plugins.qgis.org/plugins/

###### QGIS|如何正确分发成果数据？https://www.pianshen.com/article/57192006165/

表达式-函数编辑器

https://docs.qgis.org/3.16/en/docs/user_manual/working_with_vector/expression.html?highlight=function%20editor#function-editor

通过 QgsVectorLayer 的 setDiagramRenderer() 方法，传入一个 QgsDiagramRendererV2 的实例，当前的矢量图层就会以设置的方法来渲染显示图表。

其中 QgsLinearlyInterpolatedDiagramRenderer和QgsSingleCategoryDiagramRenderer 分别是具体的渲染方法类，

Diagram类

QGis当中提供的图表类包括三个，QgsPieDiagram、QgsTextDiagram 和 QgsHistogramDiagram，它们分别对应于饼状图、文本图和柱状图，都是继承自 QgsDiagram 类

具体来说，有两个，一个是QgsDiagramSettings，用于配置针对图表的参数，另一个是 QgsDiagramLayerSettings，是更高一级的参数，将图表类作为一个图层，配置与矢量要素的显示关系.

 

| **Name**                                   | **说明**                                                   |
| ------------------------------------------ | ---------------------------------------------------------- |
| angleOffset                                | 起始角度（仅饼状图）                                       |
| backgroundColor                            | 背景颜色                                                   |
| barWidth                                   | 柱宽度（仅柱状图）                                         |
| categoryAttributes  \ categoryColors       | 用于控制不同属性显示不同颜色                               |
| diagramOrientation                         | 图表方向                                                   |
| font                                       | 字体（仅文本图）                                           |
| labelPlacementMethod                       | 图表中文本的显示位置（仅文本图），包括Hight和XHight        |
| maxScaleDenominator  \ minScaleDenominator | 显示尺度。大于或小于缩放尺度，图表将不再显示               |
| minimumSize                                | 图表显示的最小尺寸。小于这个尺寸的图表会放大到这个尺寸显示 |
| penColor  \ penWidth                       | 控制轮廓的颜色和宽度                                       |
| scaleByArea                                | 是否根据要素面积进行缩放图表（仅polygon要素）              |
| size                                       | 图表大小                                                   |
| sizeType                                   | 图表大小的单位，包括 MM 和 MapUnits                        |
| transparency                               | 透明度                                                     |

 

基本代码：

```c++
void qgis_dev_layerPropDialog::setDiagramProperty()
{
    // 这一句是获取当前图层，修改成你自己获取当前图层的方式
    QgsVectorLayer* layer = qobject_cast<QgsVectorLayer*>( activeLayer() ); 
 
    QgsDiagram* diagram = 0;
    QString diagramType = "Hist"; // 这一句修改成获取图表类型的代码
    bool scaleAttributeValueOk = false;
 
    QgsVectorDataProvider* provider = layer->dataProvider();
    double maxVal = 0;
    int fld = 2;
    if ( fld != -1 )
    {
        bool ok = false;
        double val = provider->maximumValue( fld ).toDouble( &ok ); // 获取该字段的最大值
        if ( ok )
        {
            maxVal = val;
        }
    }
    bool mValueLineEdit = false;
 
    if ( diagramType == "Text" )
    {
        //diagram = new QgsTextDiagram();
    }
    else if ( diagramType == "Pie" )
    {
        //diagram = new QgsPieDiagram();
    }
    else // if ( diagramType == DIAGRAM_NAME_HISTOGRAM )
    {
        diagram = new QgsHistogramDiagram();
    }
 
    #pragma region 设置diagram属性
 
    QgsDiagramSettings ds; // diagram的设置项
    //ds.font = mDiagramFont; // 设置字体
    ds.transparency = 0; // 设置透明度
 
    QList<QColor> categoryColors; // 颜色
    QList<QString> categoryAttributes; // 属性
 
    QColor color = QColor( 255, 0, 0 );
    color.setAlpha( 255 - ds.transparency );
    categoryColors.append( color );
    categoryAttributes.append( "ELEV" );
 
    ds.categoryColors = categoryColors;
    ds.categoryAttributes = categoryAttributes;
    ds.size = QSizeF( 1, 1 );
    ds.sizeType = static_cast<QgsDiagramSettings::SizeType>( 0 );
    ds.labelPlacementMethod = static_cast<QgsDiagramSettings::LabelPlacementMethod>( 1 );
    ds.scaleByArea = true;
 
 
    ds.minimumSize = 0;
 
    ds.backgroundColor = QColor( 0, 0, 0 ); // 背景色
    ds.penColor = QColor( 0, 0, 0 ); // 轮廓颜色
    ds.penWidth = 1; // 轮廓宽度
 
    ds.minScaleDenominator = -1;
    ds.maxScaleDenominator = -1;
 
    // Diagram 方向 (histogram)
    ds.angleOffset = 1440;
    ds.diagramOrientation = static_cast<QgsDiagramSettings::DiagramOrientation>( 0 );
 
    ds.barWidth = 5; // 宽度
    #pragma endregion 设置diagram属性
 
    QgsLinearlyInterpolatedDiagramRenderer* dr = new QgsLinearlyInterpolatedDiagramRenderer();
    dr->setLowerValue( 0.0 );
    dr->setLowerSize( QSizeF( 0.0, 0.0 ) );
    dr->setUpperValue( 2 );
    dr->setUpperSize( QSizeF( 2, 2 ) );
 
    bool isExpression = true;
    dr->setClassificationAttributeIsExpression( isExpression );
 
    dr->setClassificationAttributeExpression( "" );
 
    dr->setDiagram( diagram );
    dr->setDiagramSettings( ds );
    layer->setDiagramRenderer( dr );
 
    QgsDiagramLayerSettings dls;
    dls.dist = 0;
    dls.priority = 5;
 
    dls.xPosColumn = -1;
    dls.yPosColumn = -1;
    dls.placement = static_cast<QgsDiagramLayerSettings::Placement>( 0 );
 
    dls.placementFlags = 0;
    layer->setDiagramLayerSettings( dls );
 
    layer->setTitle( "" );
 
    QgsVectorSimplifyMethod simplifyMethod = m_layer->simplifyMethod();
    simplifyMethod.setSimplifyHints( QgsVectorSimplifyMethod::NoSimplification );
    simplifyMethod.setThreshold( 1 );
    simplifyMethod.setForceLocalOptimization( true );
    simplifyMethod.setMaximumScale( 1 );
    layer->setSimplifyMethod( simplifyMethod );
 
    layer->triggerRepaint();
    m_mapCanvas->refresh(); // 改成你自己的 map canvas
}
```

