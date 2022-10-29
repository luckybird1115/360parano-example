---
title: AI组件开发（七）--AIPathSuite&&AIPathfinderSuite
date: 2019-03-25 09:11:13
tags: 
    - AI  
categories: AI组件开发
---
AIPathSuite提供的功能允许检查和操作路径,AIPathfinderSuite提供了对路径执行操作的功能，这些功能将各种效果应用于选定的路径图
<!--more-->
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [AIPathSuite](#aipathsuite)
  - [注册声明](#%E6%B3%A8%E5%86%8C%E5%A3%B0%E6%98%8E)
  - [方法](#%E6%96%B9%E6%B3%95)
  - [AIPathSegment](#aipathsegment)
  - [使用实例](#%E4%BD%BF%E7%94%A8%E5%AE%9E%E4%BE%8B)
- [AIPathfinderSuite](#aipathfindersuite)
  - [注册声明](#%E6%B3%A8%E5%86%8C%E5%A3%B0%E6%98%8E-1)
  - [方法](#%E6%96%B9%E6%B3%95-1)
  - [相关类，结构体，指针](#%E7%9B%B8%E5%85%B3%E7%B1%BB%E7%BB%93%E6%9E%84%E4%BD%93%E6%8C%87%E9%92%88)
    - [AIPathfinderData](#aipathfinderdata)
      - [AIOptions](#aioptions)
      - [AIParameters](#aiparameters)
        - [AIMixParameters](#aimixparameters)
        - [AITrapParameters](#aitrapparameters)
    - [AIFilterMessage](#aifiltermessage)
      - [SPMessageData](#spmessagedata)
      - [AIFilterHandle](#aifilterhandle)
      - [PlatformFilterParameters](#platformfilterparameters)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
# AIPathSuite
此套件提供的功能允许您检查和操作路径，这些路径是art类型的对象kPathArt。

路径的形状由一个或多个路径段定义。路径的每一个段对应于一个锚点，它是在选择路径时显示的手柄。路径上的锚点数或段 数包括每端的两个。段由AIPathSegment结构定义。段索引号从零开始。路径可以有一个锚点。

段的入点和出点定义点p处的曲线的切线。中间点不用于开放路径的初始段和最终段。段可以是角点或平滑点。对于一个角落，进出点可以在任何地方。要形成一条直线，请将每个端点的入点和出点放在它们的p点上。

路径可以是关闭的或打开的。如果路径是打开的，则仅由其段定义。如果它已关闭，Illustrator将在第一个和最后一个锚点之间绘制一个段，该段使用最后一个段的out切线和第一个段的切线。

一些路径也用作指南。在所有其他方面，它们是具有相同内容和属性的路径。绘制属性与通过路径 样式的路径相关联。见AIPathStyleSuite。

使用SPBasicSuite::AcquireSuite()常量kAIPathSuite和使用获取此套件kAIPathVersion。
## 注册声明
```
//xxxSuites.h
#include "Suites.hpp"
#include "AIArtConverter.h"
extern	"C" AIPathSuite*	 sAIPath;

//xxxSuites.cpp
extern "C"{
 AIPathSuite* sAIPath=NULL;
}

ImportSuite gImportSuites[] = {
    IMPORT_TEXT_SUITES
    kAIPathSuite,kAIPathSuiteVersion,&sAIPath,
        nil, 0, nil
      };

```
## 方法
- AIAPI AIErr（\* 	GetPathSegmentCount）（AIArtHandle path，ai :: int16 \* count）
 	获取路径上的锚点数，包括每端的两个锚点。 
- AIAPI AIErr（\* 	SetPathSegmentCount）（AIArtHandle path，ai :: int16 count）
 	从段中删除段或将段附加到路径末尾。 
- AIAPI AIErr（\* 	GetPathSegments）（AIArtHandle path，ai :: int16 segNumber，ai :: int16 count，AIPathSegment segments []）
 	从路径中检索一组段。 
- AIAPI AIErr（\* 	SetPathSegments）（AIArtHandle path，ai :: int16 segNumber，ai :: int16 count，const AIPathSegment segments []）
 	添加或更新路径中的段。 
- AIAPI AIErr（\* 	InsertPathSegments）（AIArtHandle path，ai :: int16 segNumber，ai :: int16 count，const AIPathSegment segments []）
 	将段插入路径。 
- AIAPI AIErr（\* 	DeletePathSegments）（AIArtHandle path，ai :: int16 segNumber，ai :: int16 count）
 	从路径中删除段。 
- AIAPI AIErr（\* 	GetPathClosed）（AIArtHandle path，AIBoolean \*closed）
 	报告路径是否已关闭。 
- AIAPI AIErr（\* 	SetPathClosed）（AIArtHandle path，AIBoolean closed）
 	打开或关闭路径。 
- AIAPI AIErr（\* 	GetPathGuide）（AIArtHandle path，AIBoolean \* isGuide）
 	报告路径是否用作指南。 
- AIAPI AIErr（\* 	SetPathGuide）（AIArtHandle path，AIBoolean isGuide）
 	设置是否将路径用作指南。 
- AIAPI AIErr（\* 	GetPathSegmentSelected）（AIArtHandle path，ai :: int16 segNumber，ai :: int16 \*selected）
 	报告是否选择了路径段的任何部分。 
- AIAPI AIErr（\* 	SetPathSegmentSelected）（选择AIArtHandle path，ai :: int16 segNumber，ai :: int16 selected）
 	设置段的选择状态。 
- AIAPI AIErr（\* 	ReversePathSegments）（AIArtHandle path）
 	反转路径中段的顺序。 
- AIAPI AIErr（\* 	GetPathArea）（AIArtHandle path，AIReal \*area）
 	计算路径的面积。 
- AIAPI AIErr（\* 	GetPathLength）（AIArtHandle path，AIReal \*length，AIReal flatness）
 	计算以点为单位测量的路径周长。 
- AIAPI AIErr（\* 	GetPathBezier）（AIArtHandle path，ai :: int16 segNumber，AIRealBezier \* bezier）
 	计算路径段的贝塞尔曲线值。 
- AIAPI AIErr（\* 	PathHasLength）（AIArtHandle path，AIBoolean \* hasLength）
 	报告路径是否有长度。 
- AIAPI AIErr（\* 	GetPathIsClip）（AIArtHandle path，AIBoolean \* isClip）
 	报告路径是否为剪切路径。 
- AIAPI AIErr（\* 	GetPathAllSegmentsSelected）（AIArtHandle path，选择AIBoolean \*）
 	报告是否选择了路径的所有段。 
- AIAPI AIErr（\* 	GetKeySegment）（AIArtHandle \* path，ai :: int16 \* segmentNumber）
 	检索关键锚点的路径对象和段号（即，其他锚点对齐的那个）。 
- AIAPI AIErr（\* 	CancelKeySegment）（void）
- AIAPI AIBoolean（\* 	IsPath9SliceSplitter）（AIArtHandle path）
 	报告路径是否为9切片分割器; 也就是说，该指南定义了将艺术板分成9个区域的线。 
- AIAPI AIErr（\* 	SetKeySegment）（AIArtHandle path，ai :: int16 segmentNumber）
 	设置艺术对象的关键锚点。 
- AIAPI AIErr（\* 	MeasureSegments）（AIArtHandle path，ai :: int16 startSeg，ai :: int16 count，AIReal pieceLengths []，AIReal accumulationLengths []）
 	测量路径段的长度。 
- AIAPI AIErr（\* 	LengthFractionToBezierPos）（AIArtHandle path，AIReal lengthFraction，ai :: int16＆segIndex，AIReal＆bezierT，const AIReal pieceLengths []，const AIReal accumulationLengths []）
 	计算作为沿路径的路径长度的给定分数的点的段索引和参数贝塞尔t值。 
- AIAPI AIErr（\* 	BezierPosToLengthFraction）（AIArtHandle path，ai :: int16 segIndex，AIReal bezierT，AIReal＆lengthFraction，const AIReal pieceLengths []，const AIReal accumulationLengths []）
 	计算对应于沿路径的位置的总路径长度的分数，该路径表示为段索引，并且将参数贝塞尔t值表示到该段中。 
- AIAPI AIErr（\* 	BezierPosToAnchoredPos）（AIArtHandle path，ai :: int16 segIndex，AIReal t，AIReal \* segLength，AIReal \* segLengthFraction）
 	从参数贝塞尔曲线t值转换为线段长度的一部分。 
- AIAPI AIErr（\* 	PerpendicularsAtBezierT）（AIArtHandle path，ai :: int16 segIndex，AIReal t，AIReal leftDistance，AIReal rightDistance，AIRealPoint＆pointOnPath，AIRealPoint \* leftPoint，AIRealPoint \* rightPoint）
 	计算与表示为段索引和参数贝塞尔t值的位置处的路径垂直（正常）的线段的端点
 	
## AIPathSegment
	
Contents	Suites	Classes	Class Index	Member Index
AIPathSegment Struct Reference
Defines a path segment. More...

\#include <AIPath.h>

List of all members.

Public Attributes  
AIRealPoint 	p  
AIRealPoint 	in  
AIRealPoint 	out  
AIBoolean 	corner  

## 使用实例
```
//SetPathSegmentCount
long curCount=10;//定义环面点数
AIArtHandle artGroup = NULL;
sAIArt->GetFirstArtOfLayer(PolygonLayer, &artGroup);
AIArtHandle art = NULL;
sAIArt->NewArt(kPathArt, kPlaceInsideOnTop, artGroup, &art);
sAIPath->SetPathSegmentCount(art, (short)curCount);
AIPathSegment *segment = new AIPathSegment[curCount];
AIRealPoint *AIPoint = new AIRealPoint[curCount];
...
//赋值操作
for (int i = 0; i < curCount; i++)
{
    segment[i].p.h = AIPoint[i].h;
    segment[i].p.v = AIPoint[i].v;
    segment[i].in = segment[i].out = segment[i].p;
    segment[i].corner = true;
}
//SetPathSegments
sAIPath->SetPathSegments(art, 0, curCount, segment);
delete[] segment;
delete[] AIPoint;
//SetPathClosed
sAIPath->SetPathClosed(art, 1);
arts.push_back(art);
```
# AIPathfinderSuite
该套件提供了对路径（类型的艺术对象kPathArt）执行操作的功能，这些功能将各种效果应用于选定的路径图。  
__这些操作与“路径查找器”调板和“效果”菜单中的操作相对应。__
附加功能允许操作复合 形状，可以应用效果的插件组。见AIPluginGroupSuite。在复合形状组中，绘制组中的底部对象，然后根据交互模式（AIShapeMode）由其上方的下一个对象进行修改。可以将每个后续对象添加到图形中，从图形中减去，相交或从图形中排除。

使用SPBasicSuite::AcquireSuite()常量kAIPathfinderSuite和使用获取此套件kAIPathfinderVersion。
## 注册声明
```
//xxxSuites.h
#include "Suites.hpp"
#include "AIPathfinder.h"
extern  "C" AIPathfinderSuite*          sAIPathfinder;

//xxxSuites.cpp
extern "C"{
 AIPathfinderSuite*  sAIPathfinder=NULL;
}

ImportSuite gImportSuites[] = {
    IMPORT_TEXT_SUITES
    kAIPathfinderSuite,kAIPathfinderSuiteVersion,&sAIPathfinder,
        nil, 0, nil
      };

```
## 方法
- AIAPI AIErr（\* 	DoUniteEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对选定的艺术应用Unite效果。 
- AIAPI AIErr（\* 	DoIntersectEffect）（AIPathfinderData \* data，AIFilterMessage \* message）
 	将相交效果应用于选定的art。 
- AIAPI AIErr（\* 	DoExcludeEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对所选art应用排除效果。 
- AIAPI AIErr（\* 	DoBackMinusFrontEffect）（AIPathfinderData \* data，AIFilterMessage \* message）
 	对选定的art应用前后减去效果。 
- AIAPI AIErr（\* 	DoFrontMinusBackEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对选定的art应用前后减去效果。 
- AIAPI AIErr（\* 	DoDivideEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对所选art应用分割效果。 
- AIAPI AIErr（\* 	DoOutlineEffect）（AIPathfinderData \* data，AIFilterMessage \* message）
 	将轮廓效果应用于选定的art。 
- AIAPI AIErr（\* 	DoTrimEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对所选art应用修剪效果。 
- AIAPI AIErr（\* 	DoMergeEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对选定的art应用合并效果。 
- AIAPI AIErr（\* 	DoCropEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	将裁剪效果应用于选定的art。 
- AIAPI AIErr（\* 	DoHardEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对选定的art应用硬效果。 
- AIAPI AIErr（\* 	DoSoftEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对选定的art应用柔和效果。 
- AIAPI AIErr（\* 	DoTrapEffect）（AIPathfinderData \*data，AIFilterMessage \*message）
 	对选定的art应用陷印效果。 
- AIAPI AIErr（\* 	GetHardEffectParameters）（AIPathfinderData \*data，AIFilterMessage \*message）
 	获得硬效果路径查找器操作的参数。 
- AIAPI AIErr（\* 	GetSoftEffectParameters）（AIPathfinderData \*data，AIFilterMessage \*message）
 	获得Soft效果路径查找器操作的参数。 
- AIAPI AIErr（\* 	GetTrapEffectParameters）（AIPathfinderData \* data，AIFilterMessage \* message）
 	获得陷阱效果路径查找器操作的参数。 
- AIAPI AIErr（\* 	GetGlobalOptions）（AIPathfinderData \*data，AIFilterMessage \*message）
 	获得“路径查找器”和“效果”操作的全局选项。 
AIAPI AIShapeMode（\* 	GetShapeMode）（AIArtHandle art）
 	获得art插件组对象的复合形状模式。 
- AIAPI AIErr（\* 	SetShapeMode）（AIArtHandle art，AIShapeMode mode）
 	设置art插件组对象的复合形状模式。 
- AIAPI AIErr（\* 	NewCompoundShape）（ai :: int16 paintOrder，AIArtHandle prep，AIArtHandle \* newArt）
 	在当前文档的art树的绘制顺序中的指定位置创建新的空复合形状。 
- AIAPI AIErr（\* 	IsCompoundShape）（AIArtHandle art，AIBoolean \* isCompoundShape）
 	报告art品是否为复合形状。 
- AIAPI AIErr（\* 	FlattenArt）（AIArtHandle \* inOutArt）
 	通过将Unite效果应用于每个组件art对象，将形状扩展为单个路径或复合路径。
 	
## 相关类，结构体，指针
### AIPathfinderData
AIOptions 	options  
 	Options that control how pathfinder operations are performed.   
AIParameters 	parameters  
 	Parameters for mixing and trapping operations.   
AIArtHandle * 	fSelectedArt  
 	A pointer to an array of selected art objects on which to perform the operation.   
ai::int32 	fSelectedArtCount  
 	The number of members of the fSelectedArt array.   
ai::int32 	fAlertInfoID  
 	Not used.   
#### AIOptions
控制路径查找器操作执行方式的选项。
double 	ipmPrecision  
 	Pathfinding precision, in microns.   
ai::int32 	removeRedundantPoints  
 	Nonzero to remove redundant points.   
ai::int32 	flags  
 	Option flags, a logical OR of AIOptionsFlagValues.
- AIOptionsFlagValues
    - kExtractUnpaintedArtwork 	
    设置后，从处理结果中删除未配色的图稿。  
    - kAllowEmptyOutput 	
    当设置时，这些功能可以产生空的输出：
    AIPathfinderSuite::DoBackMinusFrontEffect()，AIPathfinderSuite::DoFrontMinusBackEffect()，AIPathfinderSuite::DoIntersectEffect()，AIPathfinderSuite::DoExcludeEffect()。  
    -  kIgnoreEvenOddFillRule 	
    设置时，假设所有路径都使用非零填充规则而不是偶数填充规则。    
    - kSuppressProgressDialog 	
    设置后，在路径查找器操作期间禁止进度报告。   
    适用于AI 13及更高版本。    
    - kDeselectResultArts 	
    设置后，将取消选择结果对象。    
    适用于AI 13及更高版本。

#### AIParameters
混合和捕获参数
AIMixParameters 	mixParameters  
 	Parameters for mixing operations.   
AITrapParameters 	trapParameters  
 	Parameters for trapping operations.   
##### AIMixParameters
double 	softRate  
 	The percentage of visibility in overlapping colors.   
ai::int32 	convertCustomColors  
 	Nonzero to convert custom colors when mixing.   
##### AITrapParameters
这些对应于Pathfinder调色板的Trap对话框中提供的UI值; 有关说明，请参阅用户文档  
double 	trapThickness  
 	Trap thickness, in points.   
double 	heightWidthAspectRatio  
 	Height to width aspect ratio.   
double 	trapTint  
 	Tint.   
double 	maximumTint  
 	Maximum tint (default is recommended).   
double 	tintTolerance  
 	Tint tolerance (default is recommended).   
ai::int32 	reverseTrap  
 	Nonzero to reverse trap.   
ai::int32 	convertCustomColors  
 	Nonzero to convert custom colors.   
### AIFilterMessage
过滤器获取参数或Go选择器的消息。  
SPMessageData 	d  
 	The message data.   
AIFilterHandle 	filter  
 	The filter object.   
PlatformFilterParameters 	parameters  
 	The parameter values, in a developer-defined parameter structure.   
#### SPMessageData
ai::int32 	SPCheck  
 	kSPValidSPMessageData if this is a valid PICA message.   
struct SPPlugin \* 	self  
 	This plug-in, an SPPluginRef.   
void \* 	globals  
 	An array of application-wide global variables.   
struct SPBasicSuite \* 	basic  
 	A pointer to the basic PICA suite, which you use to obtain all other suites.   
#### AIFilterHandle
typedef struct _t_AIFilterOpaque \* 	AIFilterHandle
 	Opaque reference to a filter. 
#### PlatformFilterParameters

## 使用实例
```
//arts已定义好的art对象数组
list<AIArtHandle>::iterator artIT = arts.begin();
	if (artCount > 1){		
		AIArtHandle* artArr = (AIArtHandle *)(malloc(sizeof(AIArtHandle)*artCount));
		for (int i = 0; i < artCount; i++){
			artArr[i] = *artIT;
			artIT++;
		}
		AIFilterMessage message;
		AIPathfinderData findData;
		findData.fSelectedArt = artArr;
		findData.fSelectedArtCount = artCount;
		AIOptions option;
		option.ipmPrecision = 100;
		option.flags = kExtractUnpaintedArtwork;
		option.removeRedundantPoints = 1;
		findData.options = option;
		//应用排除效果
		sAIPathfinder->DoExcludeEffect(&findData, &message);
		delete[] artArr;
	}
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
