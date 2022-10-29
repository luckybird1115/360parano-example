
---
title: GDAL简介
date: 2019-03-18 15:26:26
tags:
    - GDAL
categories: C++
---
GDAL(Geospatial Data Abstraction Library)是一个在X/MIT许可协议下的开源栅格空间数据转换库
__持续更新中__
<!--more-->
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [简介](#%E7%AE%80%E4%BB%8B)
- [功能特征](#%E5%8A%9F%E8%83%BD%E7%89%B9%E5%BE%81)
- [OGR体系结构编辑](#ogr%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84%E7%BC%96%E8%BE%91)
- [GDAL中OGR的使用](#gdal%E4%B8%ADogr%E7%9A%84%E4%BD%BF%E7%94%A8)
  - [读数据](#%E8%AF%BB%E6%95%B0%E6%8D%AE)
  - [写数据](#%E5%86%99%E6%95%B0%E6%8D%AE)
- [参考资料：](#%E5%8F%82%E8%80%83%E8%B5%84%E6%96%99)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
# 简介
它利用抽象数据模型来表达所支持的各种文件格式。它还有一系列命令行工具来进行数据转换和处理。

OGR是GDAL项目的一个分支，功能与GDAL类似，只不过它提供对矢量数据的支持。

有很多著名的GIS类产品都使用了GDAL/OGR库，包括ESRI的ARCGIS 9.3，Google Earth和跨平台的GRASS GIS系统。利用GDAL/OGR库，可以使基于Linux的地理空间数据管理系统提供对矢量和栅格文件数据的支持。
# 功能特征
GDAL提供对多种栅格数据的支持，包括Arc/Info ASCII Grid(asc)，GeoTiff (tiff)，Erdas Imagine Images(img)，ASCII DEM(dem) 等格式。

GDAL使用抽象数据模型(abstract data model)来解析它所支持的数据格式，抽象数据模型包括数据集(dataset)，坐标系统，仿射地理坐标转换(Affine Geo Transform)， 大地控制点(GCPs)， 元数据(Metadata)，栅格波段(Raster Band)，颜色表(Color Table)，子数据集域(Subdatasets Domain)，图像结构域(Image_Structure Domain)，XML域(XML:Domains)。

GDALMajorObject类：带有元数据的对象。

GDALDdataset类：通常是从一个栅格文件中提取的相关联的栅格波段集合和这些波段的元数据;GDALDdataset也负责所有栅格波段的地理坐标转换(georeferencing transform)和坐标系定义。

GDALDriver类：文件格式驱动类，GDAL会为每一个所支持的文件格式创建一个该类的实体，来管理该文件格式。

GDALDriverManager类：文件格式驱动管理类，用来管理GDALDriver类。
# OGR体系结构编辑
Geometry类：Geometry (包括OGRGeometry等类)封装了OpenGIS的矢量数据模型，并提供了一些几何操作，WKB(Well Knows Binary)和WKT(Well Known Text)格式之间的相互转换，以及空间参考系统(投影)。

Spatial Reference类：OGRSpatialReference封装了投影和基准面的定义。

Feature类：OGRFeature封装了一个完整feature的定义，一个完整的feature包括一个geometry和geometry的一系列属性。

Feature Definition类：OGRFeatureDefn里面封装了feature的属性，类型、名称及其默认的空间参考系统等。一个OGRFeatureDefn对象通常与一个层(layer)对应。

Layer类：OGRLayer是一个抽象基类，表示数据源类OGRDataSource里面的一层要素(feature)。

Data Source类：OGRDataSource是一个抽象基类，表示含有OGRLayer对象的一个文件或一个数据库。

Drivers类：OGRSFDriver对应于每一个所支持的矢量文件格式。类OGRSFDriver由类OGRSFDriverRegistrar来注册和管理。
# GDAL中OGR的使用
## 读数据
1. 在工程的Library files中和Include files中分别添加GDAL的LIB文件目录和头文件目录

2. 最初需要注册所需的所有格式驱动程序。这通常通过调用GDALAllRegister（）来完成，该寄存器注册GDAL / OGR中内置的所有格式驱动程序。 
 
3. 接下来我们需要打开输入OGR数据源。数据源可以是文件，RDBMS，充满文件的目录，甚至是远程Web服务，具体取决于所使用的驱动程序。但是，数据源名称始终是单个字符串。在这种情况下，我们硬编码打开一个特定的shapefile。第二个参数（GDAL_OF_VECTOR）告诉OGROpen（）方法我们想要使用向量驱动程序并且不需要更新访问。失败时返回NULL，我们报告错误。

4. GDALDataset可以潜在地具有与它相关联的许多层。可以使用GDALDataset :: GetLayerCount（）查询可用的图层数，并使用GDALDataset :: GetLayer（）通过索引获取各个图层。但是，我们只是按名称获取图层。  
    OGRLayer   * poLayer;
    poLayer = poDS-> GetLayerByName（“point”）;  

5. 现在我们要开始从图层中读取要素。在我们开始之前，我们可以为图层指定属性或空间过滤器以限制我们获取的功能集，但是现在我们有兴趣获取所有功能。

6. 我们使用OGRLayer :: GetNextFeature（）遍历图层中的所有要素。当我们用完功能时它会返回NULL。
    ```
    OGRFeature *poFeature;
    poLayer->ResetReading();
    while( (poFeature = poLayer->GetNextFeature()) != NULL )
    {
    ```
7. 为了转储该功能的所有属性字段，获取OGRFeatureDefn会很有帮助。这是与图层关联的对象，包含所有字段的定义。我们遍历所有字段，并根据其类型获取和报告属性。
    ```
    for( auto&& oField: *poFeature )
    {
        switch( oField.GetType() )
        {
            case OFTInteger:
                printf( "%d,", oField.GetInteger() );
                break;
            case OFTInteger64:
                printf( CPL_FRMT_GIB ",", oField.GetInteger64() );
                break;
            case OFTReal:
                printf( "%.3f,", oField.GetDouble() );
                break;
            case OFTString:
                printf( "%s,", oField.GetString() );
                break;
            default:
                printf( "%s,", oField.GetAsString() );
                break;
        }
    }
    ```
    除了上面显式处理的字段类型之外，还有一些字段类型，但可以使用OGRFeature :: GetFieldAsString（）方法获取它们的合理表示。事实上，我们可以通过对所有类型使用OGRFeature :: GetFieldAsString（）来缩短上述内容。   
8. 接下来，我们要从要素中提取几何体，并写出点几何体x和y。几何图形作为通用OGRGeometry指针返回。然后我们确定特定的几何类型，如果它是一个点，我们将其转换为点并对其进行操作。如果它是别的东西我们写占位符。
    ```
        OGRGeometry *poGeometry;
        poGeometry = poFeature->GetGeometryRef();
        if( poGeometry != NULL
                && wkbFlatten(poGeometry->getGeometryType()) == wkbPoint )
        {
    #if GDAL_VERSION_NUM >= GDAL_COMPUTE_VERSION(2,3,0)
            OGRPoint *poPoint = poGeometry->toPoint();
    #else
            OGRPoint *poPoint = (OGRPoint *) poGeometry;
    #endif
            printf( "%.3f,%3.f\n", poPoint->getX(), poPoint->getY() );
        }
        else
        {
            printf( "no point geometry\n" );
        }
    ```
    所述wkbFlatten（）宏上述用于将类型转换为一个wkbPoint25D（带z坐标的点）到2D的类型代码（wkbPoint）。对于每个2D几何类型，都有相应的2.5D类型代码。2D和2.5D几何案例由相同的C ++类处理，因此我们的代码将正确处理2D或3D案例。
    ```
     OGRGeometry *poGeometry;
        int iGeomField;
        int nGeomFieldCount;
        nGeomFieldCount = poFeature->GetGeomFieldCount();
        for(iGeomField = 0; iGeomField < nGeomFieldCount; iGeomField ++ )
        {
            poGeometry = poFeature->GetGeomFieldRef(iGeomField);
            if( poGeometry != NULL
                    && wkbFlatten(poGeometry->getGeometryType()) == wkbPoint )
            {
            #if GDAL_VERSION_NUM >= GDAL_COMPUTE_VERSION(2,3,0)
                        OGRPoint *poPoint = poGeometry->toPoint();
            #else
                        OGRPoint *poPoint = (OGRPoint *) poGeometry;
            #endif
                        printf( "%.3f,%3.f\n", poPoint->getX(), poPoint->getY() );
                    }
            else
            {
                printf( "no point geometry\n" );
            }
        }
    ```
    请注意，OGRFeature :: GetGeometryRef（）和OGRFeature :: GetGeomFieldRef（）返回指向OGRFeature所拥有的内部几何的指针。实际上没有删除返回几何。  
9. 对于GDAL <2.3，因为OGRLayer :: GetNextFeature（）方法返回现在由我们拥有的功能的副本。因此，在使用结束时，我们必须释放该功能。我们可以“删除”它，但这可能会导致Windows版本中的问题，其中GDAL DLL与主程序具有不同的“堆”。为了安全起见，我们使用GDAL功能删除该功能。  
    `   OGRFeature :: DestroyFeature（poFeature）;}`
10. 该OGRLayer通过返回的GDALDataset :: GetLayerByName（）也是由拥有一个内部层参考的GDALDataset所以我们并不需要将其删除。但我们确实需要删除数据源才能关闭输入文件。我们再一次使用自定义删除方法来避免特殊的win32堆问题。  
     `    GDALClose（poDS）; }`
11. 完整模板 
    ```
    #include "ogrsf_frmts.h"
    int main()
    {
        GDALAllRegister();
        GDALDataset *poDS = static_cast<GDALDataset*>(
            GDALOpenEx( "point.shp", GDAL_OF_VECTOR, NULL, NULL, NULL ));
        if( poDS == NULL )
        {
            printf( "Open failed.\n" );
            exit( 1 );
        }
        OGRLayer  *poLayer = poDS->GetLayerByName( "point" );
        OGRFeatureDefn *poFDefn = poLayer->GetLayerDefn();
        poLayer->ResetReading();
        OGRFeature *poFeature;
        while( (poFeature = poLayer->GetNextFeature()) != NULL )
        {
            for( int iField = 0; iField < poFDefn->GetFieldCount(); iField++ )
            {
                OGRFieldDefn *poFieldDefn = poFDefn->GetFieldDefn( iField );
                switch( poFieldDefn->GetType() )
                {
                    case OFTInteger:
                        printf( "%d,", poFeature->GetFieldAsInteger( iField ) );
                        break;
                    case OFTInteger64:
                        printf( CPL_FRMT_GIB ",", poFeature->GetFieldAsInteger64( iField ) );
                        break;
                    case OFTReal:
                        printf( "%.3f,", poFeature->GetFieldAsDouble(iField) );
                        break;
                    case OFTString:
                        printf( "%s,", poFeature->GetFieldAsString(iField) );
                        break;
                    default:
                        printf( "%s,", poFeature->GetFieldAsString(iField) );
                        break;
                }
            }
            OGRGeometry *poGeometry = poFeature->GetGeometryRef();
            if( poGeometry != NULL
                    && wkbFlatten(poGeometry->getGeometryType()) == wkbPoint )
            {
                OGRPoint *poPoint = (OGRPoint *) poGeometry;
                printf( "%.3f,%3.f\n", poPoint->getX(), poPoint->getY() );
            }
            else
            {
                printf( "no point geometry\n" );
            }
            OGRFeature::DestroyFeature( poFeature );
        }
        GDALClose( poDS );
    }
    ```
## 写数据
 作为通过OGR写作的一个例子，我们将大致与上述相反。从输入文本中读取逗号分隔值的短程序将通过OGR写入点shapefile。  
    1. 像往常一样，我们首先注册所有驱动程序，然后获取Shapefile驱动程序，因为我们需要它来创建输出文件。
        ```
        #include "ogrsf_frmts.h"
        int main()
        {
            const char *pszDriverName = "ESRI Shapefile";
            GDALDriver *poDriver;
            GDALAllRegister();
            poDriver = GetGDALDriverManager()->GetDriverByName(pszDriverName );
            if( poDriver == NULL )
            {
                printf( "%s driver not available.\n", pszDriverName );
                exit( 1 );
            }
        ```
    2. 接下来我们创建数据源。ESRI Shapefile驱动程序允许我们创建一个完整的shapefile目录，或一个shapefile作为数据源。在这种情况下，我们将通过在名称中包含扩展名来显式创建单个文件。其他驱动程序表现不同。第二个，第三个，第四个和第五个参数与栅格尺寸相关（如果驱动程序具有栅格功能）。调用的最后一个参数是选项值列表，但在这种情况下我们将只使用默认值。支持的选项的详细信息也是特定于格式的。
        ```
        GDALDataset *poDS;
        poDS = poDriver->Create( "point_out.shp", 0, 0, 0, GDT_Unknown, NULL );
        if( poDS == NULL )
        {
            printf( "Creation of output file failed.\n" );
            exit( 1 );
        }
        ```
    3. 现在我们创建输出层。在这种情况下，由于数据源是单个文件，因此我们只能有一个图层。我们传递wkbPoint来指定该层支持的几何类型。在这种情况下，我们不传递任何坐标系信息或其他特殊图层创建选项。
        ```
        OGRLayer *poLayer;
        poLayer = poDS->CreateLayer( "point_out", NULL, wkbPoint, NULL );
        if( poLayer == NULL )
        {
            printf( "Layer creation failed.\n" );
            exit( 1 );
        }
        ```
    4. 现在该图层已存在，我们需要创建应出现在图层上的任何属性字段。必须在写入任何要素之前将字段添加到图层。要创建字段，我们使用有关字段的信息初始化OGRField对象。在Shapefiles的情况下，字段宽度和精度在创建输出.dbf文件时很重要，所以我们专门设置它，尽管通常默认值是OK。对于此示例，我们将只有一个属性，一个与x，y点关联的名称字符串。
       
       请注意，我们传递给CreateField（）的模板OGRField是在内部复制的。我们保留对象的所有权。
       ```
       OGRFieldDefn oField（“Name”，OFTString）;
       oField.SetWidth（32）;
       if（poLayer-> CreateField（＆oField）！= OGRERR_NONE）
       {
           printf（“创建名称字段失败。\ n”）;
           退出（1）;
       }
       //通过读数据中的x,y,name，写入
       double x, y;
        char szName[33];
        while( !feof(stdin)
               && fscanf( stdin, "%lf,%lf,%32s", &x, &y, szName ) == 3 )
        {
       
       ```
    5. 要将功能写入磁盘，我们必须创建本地OGRFeature，设置属性并附加几何体，然后再尝试将其写入图层。必须从与要写入的层关联的OGRFeatureDefn实例化此功能。
        ```
        OGRFeature * poFeature;
        poFeature = OGRFeature :: CreateFeature（poLayer-> GetLayerDefn（））;
        poFeature-> SetField（“Name”，szName）;
        ```
    6. 我们创建一个本地几何对象，并将其副本（间接）分配给该功能。的和OGRFeature :: SetGeometryDirectly（）不同于和OGRFeature :: SetGeometry（）在该直接方法给出了几何形状的特征的所有权。这通常更有效，因为它避免了几何体的额外深层对象副本。
        ```
        OGRPoint pt;
        pt.setX( x );
        pt.setY( y );
        poFeature->SetGeometry( &pt );
        ```
    7. 现在我们在文件中创建一个功能。该OGRLayer :: CreateFeature（）不走我们的特点的所有权，所以我们在用它做清理。
        ```
         if( poLayer->CreateFeature( poFeature ) != OGRERR_NONE )
             {
                 printf( "Failed to create feature in shapefile.\n" );
                exit( 1 );
             }
             OGRFeature::DestroyFeature( poFeature );
        }
        ```
    8. 最后，我们需要关闭数据源，以确保以有序的方式写出标头，并恢复所有资源。`GDALClose（poDS）;}`
    9. 模板
        ```
        #include "ogrsf_frmts.h"
        int main()
        {
            const char *pszDriverName = "ESRI Shapefile";
            GDALDriver *poDriver;
            GDALAllRegister();
            poDriver = GetGDALDriverManager()->GetDriverByName(pszDriverName );
            if( poDriver == NULL )
            {
                printf( "%s driver not available.\n", pszDriverName );
                exit( 1 );
            }
            GDALDataset *poDS;
            poDS = poDriver->Create( "point_out.shp", 0, 0, 0, GDT_Unknown, NULL );
            if( poDS == NULL )
            {
                printf( "Creation of output file failed.\n" );
                exit( 1 );
            }
            OGRLayer *poLayer;
            poLayer = poDS->CreateLayer( "point_out", NULL, wkbPoint, NULL );
            if( poLayer == NULL )
            {
                printf( "Layer creation failed.\n" );
                exit( 1 );
            }
            OGRFieldDefn oField( "Name", OFTString );
            oField.SetWidth(32);
            if( poLayer->CreateField( &oField ) != OGRERR_NONE )
            {
                printf( "Creating Name field failed.\n" );
                exit( 1 );
            }
            double x, y;
            char szName[33];
            while( !feof(stdin)
                   && fscanf( stdin, "%lf,%lf,%32s", &x, &y, szName ) == 3 )
            {
                OGRFeature *poFeature;
                poFeature = OGRFeature::CreateFeature( poLayer->GetLayerDefn() );
                poFeature->SetField( "Name", szName );
                OGRPoint pt;
                pt.setX( x );
                pt.setY( y );
                poFeature->SetGeometry( &pt );
                if( poLayer->CreateFeature( poFeature ) != OGRERR_NONE )
                {
                    printf( "Failed to create feature in shapefile.\n" );
                    exit( 1 );
                }
                OGRFeature::DestroyFeature( poFeature );
            }
            GDALClose( poDS );
        }
        ```
# 参考资料：
1. 百度百科：https://baike.baidu.com/item/GDAL/4004525?fr=aladdin
2. 官方网站：https://www.gdal.org/
