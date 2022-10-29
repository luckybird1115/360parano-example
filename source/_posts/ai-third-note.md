---
title: AI组件开发（三）--AIArtboardSuite
date: 2019-03-24 16:53:24
tags: 
  - AI  
categories: AI组件开发
---
AIArtboardSuite此套件提供的实用程序允许在文档中创建和操作Artboards。相关类ArtboardProperties和ArtboardList
<!--more-->
# AIArtboardSuite
此套件提供的实用程序允许您在文档中创建和操作画板Artboards。文档与ArtboardList包含多个Artboard对象的对象相关联，每个ArtboardProperties对象与对象相关联。

建议您使用的包装类，ai::ArtboardProperties和ai::ArtboardList而不是直接调用这些功能。

使用SPBasicSuite::AcquireSuite()常量kAIArtboardSuite和使用获取此套件kAIArtboardSuiteVersion。
```
//xxxSuites.h
#include "AIArtboard.h"
extern	"C" AIArtboardSuite*	sAIArtboard;

//xxxSuites.cpp
extern "C"{
AIArtboardSuite*				sAIArtboard = NULL;
}

ImportSuite gImportSuites[] = {
    IMPORT_TEXT_SUITES
    kAIArtboardSuite, kAIArtboardSuiteVersion, &sAIArtboard,
        nil, 0, nil
      };
```

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**目录**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [AddNew](#addnew)
- [CLoneArtboard](#cloneartboard)
- [3.Delete](#3delete)
- [4.Dispose](#4dispose)
- [5.GetActive](#5getactive)
- [6.GetArtboardProperties](#6getartboardproperties)
- [7.GetCount](#7getcount)
- [8.GetName](#8getname)
- [9.GetPAR](#9getpar)
- [10.GetPosition](#10getposition)
- [11.GetRulerOrigin](#11getrulerorigin)
- [12.GetShowDisplayMark](#12getshowdisplaymark)
- [13.Init](#13init)
- [14.Insert](#14insert)
- [15.IsDefaultName](#15isdefaultname)
- [16.ReleaseArtboardList](#16releaseartboardlist)
- [17. SetActive](#17-setactive)
- [18.SetIsDefaultName](#18setisdefaultname)
- [19.SetName](#19setname)
- [20.SetPAR](#20setpar)
- [21.SetPosition](#21setposition)
- [22.SetRulerOrigin](#22setrulerorigin)
- [23.SetShowDisplayMark](#23setshowdisplaymark)
- [24.Update](#24update)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
## AddNew
AIAPI AIErr（* AIArtboardSuite :: AddNew）（ai :: ArtboardList＆artboardList，ai :: ArtboardProperties＆newArtboard，ai :: ArtboardID ＆index）
将新画板添加到当前文档并在画板列表中报告其索引位置。
- 参数：    
    artboardList 	画板列表对象。  
    newArtboard 	新画板的属性对象。  
    index 	__[out\]__一个缓冲区，用于返回新画板的从0开始的索引位置。  
- 返回：
    kAIExceededMaxArtboardLimitErr列表大小超过应用程序定义的限制时的错误。  
   
参考ai :: ArtboardList :: AddNew（）
## CLoneArtboard
AIAPI AIErr（* AIArtboardSuite :: CloneArtboard）（ai :: ArtboardProperties &artboard，const ai :: ArtboardProperties &newArtboard）
复制画板对象。

- 参数：  
    artboard 	artboard属性要克隆。  
    newArtboard 	__[out\]__用于返回新画图属性对象。 
     
参考ai :: ArtboardProperties :: ArtboardProperties()
## 3.Delete
AIAPI AIErr（* AIArtboardSuite ::Delete）（ai :: ArtboardList &artboardList，ai :: ArtboardID &index）
从画板列表中删除画板，并使列表中的下一个画板处于活动状态。

无法删除最后一个画板。

- 参数：  
    artboardList 	画板列表对象。  
    index 	要从列表中删除的画板的从0开始的索引位置。  
- 返回：  
    kAICantDeleteLastArtboardErr如果您尝试删除最后一个画板时出现错误。  
    
参考ai::ArtboardList::Delete()。
## 4.Dispose
AIAPI AIErr(* AIArtboardSuite::Dispose)(ai::ArtboardProperties &properties)
释放画板属性对象。
- 参数：
  properties 	画板属性对象。    
  
参考ai :: ArtboardProperties :: ~ArtboardProperties（）
## 5.GetActive
AIAPI AIErr（* AIArtboardSuite :: GetArtboardList）（ai :: ArtboardList ＆artboardList）
获得当前文档的画板列表。
- 参数：  
   artboardList 	__[out\]__用于返回画板列表对象的引用。   
   
参考ai :: ArtboardList :: ArtboardList（）。
## 6.GetArtboardProperties
AIAPI AIErr（* AIArtboardSuite :: GetArtboardProperties）（ai :: ArtboardList &artboardList，ai :: ArtboardID index，ai :: ArtboardProperties &properties）
从画板列表中获得画板的属性。

使用GetCount()和Update()修改画板而不更改文档中哪个画板处于活动状态。

- 参数：
    artboardList 	画板列表对象。  
    index 	列表中画板的从0开始的索引位置。  
    properties 	__[out\]__用于返回画板属性对象。出错时，返回无效对象
- 使用：  
    ```
        ai::ArtboardProperties tempArtboardProperties;
        ai::ArtboardList tempoartboardList;
        sAIArtboard->GetArtboardList(tempoartboardList);
        sAIArtboard->GetArtboardProperties(tempoartboardList, 0, tempArtboardProperties);
    ```
## 7.GetCount  
AIAPI AIErr（* AIArtboardSuite :: GetCount）（const ai :: ArtboardList &artboardList，ai :: ArtboardID &count）
获得画板列表中定义的画板数量。

- 参数：  
    artboardList 	画板列表对象。
    count 	__[out\]__用于返回画板数量。
    
参考ai :: ArtboardList :: GetCount（）。
## 8.GetName
AIAPI AIErr(* AIArtboardSuite::GetName)(const ai::ArtboardProperties & properties, ai::UnicodeString &name)
## 9.GetPAR
AIAPI AIErr(* AIArtboardSuite::GetPAR)(const ai::ArtboardProperties &properties, AIReal &par)
获得画板标尺的像素长宽比。
- 参数：
    properties 	画板属性对象。
    par 	__[out\]__用于返回像素宽高比。
- 实例：
    ```
        ai::ArtboardProperties tempArtboardProperties;
        ai::ArtboardList tempoartboardList;
        sAIArtboard->GetArtboardList(tempoartboardList);
        sAIArtboard->GetArtboardProperties(tempoartboardList, 0, tempArtboardProperties);
        AIReal artPar;
        sAIArtboard->GetPAR(tempArtboardProperties,artPar)
    ```
## 10.GetPosition
AIAPI AIErr（* AIArtboardSuite :: GetPosition）（const ai :: ArtboardProperties＆properties，AIRealRect＆bounds）  
获得画板的位置和边界。
- 参数：
    properties 	画板属性对象。
    bounds 	__[out\]__一个矩形对象，用于返回画板的位置和边界。
- 实例：
    ```
        ai::ArtboardProperties tempArtboardProperties;
        ai::ArtboardList tempoartboardList;
        sAIArtboard->GetArtboardList(tempoartboardList);
        sAIArtboard->GetArtboardProperties(tempoartboardList, 0, tempArtboardProperties);
        AIRealRect Bound;
        sAIArtboard->GetPosition(tempArtboardProperties, Bound);      
        ASReal bottom = Bound.bottom;
        ASReal top = Bound.top;
        ASReal right = Bound.right;
        ASReal left = Bound.left;
    ```
## 11.GetRulerOrigin
AIAPI AIErr（* AIArtboardSuite :: GetRulerOrigin）（const ai :: ArtboardProperties＆properties，AIRealPoint＆rulerOrigin）
获得相对于左下角的画板的标尺原点。
- 参数：
    properties 	画板属性对象。
    rulerOrigin 	__[out\]__用于返回标尺原点。
    
## 12.GetShowDisplayMark
AIAPI AIErr（* AIArtboardSuite :: GetShowDisplayMark）（const ai :: ArtboardProperties＆properties，ai :: ArtboardProperties :: DisplayMarkType type，AIBoolean＆show）
报告当前是否显示特定类型的画板注释。
- 参数：  
    properties 	画板属性对象。
    type 	注释类型。
    show 	如果显示类型则返回true，如果隐藏则返回false。
    
## 13.Init
AIAPI AIErr（* AIArtboardSuite :: Init）（ai :: ArtboardProperties＆artboard）
使用默认值初始化画板属性对象。

- 参数：  
  artboard 	画板属性对象。
  
## 14.Insert
AIAPI AIErr（* AIArtboardSuite :: Insert）（ai :: ArtboardList＆artboardList，ai :: ArtboardProperties＆artboard，ai :: ArtboardID＆index）
在指定位置插入新画板。

- 参数：  
    artboardList 	ArtboardList对象。
    artboard 	新画板的属性。
    index 	基于0的新画板的索引位置。
- 返回：
    kAIExceededMaxArtboardLimitErr如果超出允许的最大画板数，则会出错。
    
## 15.IsDefaultName
AIAPI AIErr（* AIArtboardSuite :: IsDefaultName）（const ai :: ArtboardProperties＆properties，AIBoolean＆isDefault）
报告画板的当前名称是否是应用程序生成的。

在这种情况下，用户选择将画板内容导出为JPEG，并将画板保存为单独的文件时，文件只能通过编号来区分，而不是完整生成的名称; 例如，myArt-01.jpg。如果已明确设置画板名称，则会在导出的JPEG文件名中使用该名称。

- 参数：  
    properties 	画板属性对象。
    isDefault 	如果生成名称则返回true的缓冲区，如果已显式设置，则返回false。
    
## 16.ReleaseArtboardList
AIAPI AIErr(* AIArtboardSuite::ReleaseArtboardList)(ai::ArtboardList &artboardList)
释放画板列表对象。

- 参数：  
    artboardList 	画板列表对象。
    
## 17. SetActive
AIAPI AIErr（* AIArtboardSuite :: SetActive）（ai :: ArtboardList＆artboardList，ai :: ArtboardID index）
使特定画板处于活动状态，并使其按迭代顺序显示。
- 参数：  
    artboardList 	画板列表对象。  
    index 	列表中画板的从0开始的索引位置。  
    
## 18.SetIsDefaultName
AIAPI AIErr（* AIArtboardSuite :: SetIsDefaultName）（ai :: ArtboardProperties＆properties，const AIBoolean＆isDefault）

## 19.SetName
AIAPI AIErr(* AIArtboardSuite::SetName)(ai::ArtboardProperties &properties, const ai::UnicodeString &name)
## 20.SetPAR
AIAPI AIErr（* AIArtboardSuite :: SetPAR）（ai :: ArtboardProperties＆properties，AIReal par）
修改画板标尺的像素长宽比。

AIArtboardSuite::Update()使用此对象调用进行更改。

- 参数：
    properties 	画板属性对象。  
    par 	新的像素长宽比。  
    
## 21.SetPosition
AIAPI AIErr（* AIArtboardSuite :: SetPosition）（ai :: ArtboardProperties＆properties，const AIRealRect＆bounds）
修改画板的位置和边界。

AIArtboardSuite::Update()使用此对象调用进行更改。

- 参数：
    properties 	画板属性对象  
    bounds 	新的位置和边界  
    
## 22.SetRulerOrigin
AIAPI AIErr（* AIArtboardSuite :: SetRulerOrigin）（ai :: ArtboardProperties＆properties，const AIRealPoint＆rulerOrigin）
设置画板的标尺原点，相对于左下角。

- 参数：
  properties 	画板属性对象 
  rulerOrigin 	标尺原点
  
## 23.SetShowDisplayMark
AIAPI AIErr（* AIArtboardSuite :: SetShowDisplayMark）（ai :: ArtboardProperties＆properties，ai :: ArtboardProperties :: DisplayMarkType type，AIBoolean show）
设置当前是否显示特定类型的画板注释。
## 24.Update
AIAPI AIErr（* AIArtboardSuite :: Update）（ai :: ArtboardList＆artboardList，ai :: ArtboardID index，const ai :: ArtboardProperties＆properties）
更新画板的属性。

使用GetCount()和GetArtboardProperties()修改画板而不更改文档中哪个画板处于活动状态。

- 参数：
    artboardList 	画板列表对象。
    index 	文档列表中画板的从0开始的索引位置。
    properties 	包含新画板属性的对象。
- 实例：
   ```
    AIRealRect DateBound;
    AIRealPoint CenterPt;
    double m_scale=2000
    DateBound.top = (this->DateBound.top - this->m_center.v) * 1000 / m_scale * (72 / 25.4) - AIcenter.v;
   	DateBound.bottom = (this->DateBound.bottom - this->m_center.v) * 1000 / m_scale * (72 / 25.4) - AIcenter.v;
   	DateBound.left = (this->DateBound.left - this->m_center.h) * 1000 / m_scale * (72 / 25.4) - AIcenter.h;
   	DateBound.right = (this->DateBound.right - this->m_center.h) * 1000 / m_scale * (72 / 25.4) - AIcenter.h;
   
   	CenterPt.h = (DateBound.left - DateBound.right) / 2;
   	CenterPt.v = (DateBound.top - DateBound.bottom) / 2;
   
   	sAIArtboard->SetPosition(tempArtboardProperties, DateBound);//*******
   	sAIArtboard->Update(tempoartboardList, 0, tempArtboardProperties);//*******
   ```
# 相关文章
[AI组件开发（一）--注记旋转矩阵](/2019/03/14/ai-first-note)  
[AI组件开发（二）--AIRealMathSuite](/2019/03/18/ai-second-note)
[AI组件开发（三）--AIArtboardSuite](/2019/03/24/ai-third-note)
[AI组件开发（四）--AIDocumentSuite](/2019/03/24/ai-forth-document-note)  
[AI组件开发（五）--AILayerSuite](/2019/03/24/ai-fifth-ailayer-note)
[AI组件开发（六）--AIArtSuite](/2019/03/24/ai-six-aiart-note)
[AI组件开发（七）--AIPathSuite&&AIPathfinderSuite](/2019/03/25/ai-seven-path-note)  
[AI组件开发（八）--AIArtStyleSuite](/2019/03/25/ai-eight-style-note)
