---
title: AE开发问题总结(一)
date: 2019-03-20 12:55:36
tags:
     - ArcEngine
     - VS
categories: ArcGIS
---
今天本地运行项目，莫名其妙地报了个 尝试读取或写入受保护的内存，这通常指示其他内存已损坏！提供解决方案
<!--more-->
# AE 读Shp文件及查属性表等
```
 try
{
    bool flag = true;
     IWorkspaceFactory pWsFactory = new ShapefileWorkspaceFactoryClass();
     IFeatureWorkspace pWorkSpace = pWsFactory.OpenFromFile(path, 0) as IFeatureWorkspace;//path为shp文件所在目录路径
     IFeatureClass pFeatureClass = pWorkSpace.OpenFeatureClass(System.IO.Path.GetFileNameWithoutExtension(path));//OpenFeatureClass的参数为不带后缀的shp文件名
     //检查属性                               
     IFeatureLayer pFeatureLayer = new FeatureLayerClass();
     pFeatureLayer.Name = pFeatureClass.AliasName;
     pFeatureLayer.FeatureClass = pFeatureClass;
    
     //查询 ,search的参数第一个为过滤条件，第二个为是否重复执行
     IFeatureCursor pFeatureCursor = pFeatureLayer.Search(null, false);
     //获取查询到的要素 
     IFeature pFeature = pFeatureCursor.NextFeature();
     string strFeatureClassName = (pFeatureClass as IDataset).Name;
     int fieldCount = pFeatureClass.Fields.FieldCount;
     
     else
     {
         for (int i = 0; i < fieldCount; ++i)
         {
             //这段当时的需求是判断shp文件中是否有规定要求的字段，
             string fieldName = ConstantInfo.shpFields[i];
             if (pFeatureClass.Fields.FindField(fieldName) == -1)
             {
                 //自定义函数，按照需要修改
                 flag = false;
             }
    
         }
     }
     IFeature pTempFeature = pFeatureCursor.NextFeature();
     int rowid = 0;
     while (null != pTempFeature)
     {
         rowid++;
         for (int i = 0; i < fieldCount; ++i)
         {
             string str = Convert.ToString(pTempFeature.get_Value(i));
             //空值检查
             if (str == "")
             {
                 /自定义函数，按照需要修改
                 flag = false;
             }
    
         }
         pTempFeature = pFeatureCursor.NextFeature();
     }
     Marshal.ReleaseComObject(pFeatureCursor);
     if (!flag)
     {
         MainForm.isPassed = false;
         logger.Error("Error:" + childNode.InnerText.Replace("%%", this.strPac) + "shp文件属性等检查未通过\n");
     }

}
catch (Exception e)
{
 //自定义函数，按照需要修改
}
break;
```
# 尝试读取或写入受保护的内存，这通常指示其他内存已损坏【解决方法】
以__管理员的身份__运行CMD   执行netsh winsock reset    然后提示你重新启动计算机   重启后错误就没有了

但这样太过复杂，没有从程序自身角度解决这个问题，继续查阅资料，最终找到了有效的方法，释放程序锁
# 最佳解决方法
使用IWorkspaceFactoryLockControl接口的SchemaLockingEnabled和DisableSchemaLocking，分别获得工作空间锁状态，并关闭，具体代码如下：
```
IWorkspaceFactory pWsFactory = new ShapefileWorkspaceFactory();
//关闭资源锁定   
IWorkspaceFactoryLockControl ipWsFactoryLock = (IWorkspaceFactoryLockControl)pWsFactory;
if (ipWsFactoryLock.SchemaLockingEnabled)
{
  ipWsFactoryLock.DisableSchemaLocking();
            }
```
  

# 错误原因分析
   AE中非托管变量未释放，这些有Icursor，IFeatureCursor，IEnumStyleGalleryItem，IEnumBSTR，IStyleGallery；AGS里面有IServerContext等，释放方法System.Runtime.InteropServices.Marshal.ReleaseComObject(o);或者使用ESRI.ArcGIS.ADF.COMSupport.AOUninitialize.Shutdown()  

参考：[1] https://blog.csdn.net/mattran/article/details/47149077   
      [2] https://blog.csdn.net/u011116642/article/details/17960135  
