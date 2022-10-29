---
title: AI组件开发（六）--AIArtSuite
date: 2019-03-24 21:43:22
tags: 
    - AI  
categories: AI组件开发
---
Adobe Illustrator 文档中绘制的所有图形均为Art，相关操作函数在AIArtSuite中定义
<!--more-->
# AIArtSuite
此套件允许您访问和修改Illustrator文档中的图稿。

它提供了创建和删除对象，重新排列对象以及获取和设置对象信息的功能。您可以使用这些函数来导航图形对象树，以及获得或修改有关图形对象的一般信息，例如它们的边界或锁定状态。

Art套件是实现大多数插件的基础。Illustrator文档由一个由a引用的图形对象的集合组成AIArtHandle。这是指向文档图稿数据库中图形对象的不透明指针。通过Art suite的访问器功能访问这些字段。

艺术对象可以表示单个实体，诸如路径，文本块或放置的图像，或一组对象，诸如路径的集合。艺术类型标识符（AIArtType）表示艺术品的类型。

使用SPBasicSuite::AcquireSuite()常量kAIArtSuite和使用获取此套件kAIArtVersion。
## 注册声明
```
//xxxSuites.h
#include "Suites.hpp"
#include "AIArtConverter.h"
extern  "C" AAIArtSuite*	sAIArt ;

//xxxSuites.cpp
extern "C"{
AIArtSuite*	sAIArt = NULL;
}

ImportSuite gImportSuites[] = {
    IMPORT_TEXT_SUITES
    kAIArtSuite, kAIArtSuiteVersion, &sAIArt,
        nil, 0, nil
      };

```
## 方法
- AIAPI AIErr（\* 	NewArt）（ai :: int16类型，ai :: int16 paintOrder，AIArtHandle prep，AIArtHandle \* newArt）
 	创建一个新的Art对象。 
- AIAPI AIErr（\* 	DisposeArt）（AIArtHandle Art）
 	从文档中删除Art对象。 
- AIAPI AIErr（\* 	ReorderArt）（AIArtHandle thisArt，ai :: int16 paintOrder，AIArtHandle prep）
 	更改Art对象的绘制顺序，或将路径移入或移出复合路径或组。 
- AIAPI AIErr（\* 	DuplicateArt）（AIArtHandle thisArt，ai :: int16 paintOrder，AIArtHandle prep，AIArtHandle \* newArt）
 	使用深层复制复制Art对象（组和复合路径的子项也是重复的）。 
- AIAPI AIErr（\* 	GetFirstArtOfLayer）（AILayerHandle图层，AIArtHandle \*Art）
 	获得图层中的第一个Art对象，该图层包含该图层中的所有Art。 
- AIAPI AIErr（\* 	GetLayerOfArt）（AIArtHandle art，AILayerHandle \*layer）
 	获得Art对象的父图层（如果有）。 
- AIAPI AIErr（\* 	GetArtType）（AIArtHandle art，short \* type）
 	获得Art对象的类型。 
- AIAPI AIErr（\* 	GetArtUserAttr）（AIArtHandle art，ai :: int32 whichAttr，ai :: int32 \* attr）
 	获得Art对象的用户属性; 也就是说，标志显示它是被选中，隐藏还是被锁定。 
- AIAPI AIErr（\* 	SetArtUserAttr）（AIArtHandle art，ai :: int32 whichAttr，ai :: int32 attr）
 	设置对象的用户属性; 也就是说，标志显示它是被选中，隐藏还是被锁定。 
- AIAPI AIErr（\* 	GetArtParent）（AIArtHandle art，AIArtHandle \*parent）
 	获得Art对象的父组。 
- AIAPI AIErr（\* 	GetArtFirstChild）（AIArtHandle art，AIArtHandle \*child）
 	获得组对象的第一个子对象。 
- AIAPI AIErr（\* 	GetArtSibling）（AIArtHandle art，AIArtHandle \*sibling）
 	获得同一组中的下一个对象。 
- AIAPI AIErr（\* 	GetArtPriorSibling）（AIArtHandle art，AIArtHandle \*sibling）
 	获得同一组中的上一个对象。 
- AIAPI AIErr（\* 	GetArtBounds）（AIArtHandle art，AIRealRect \* bounds）
 	获得Art对象的包围矩形。 
- AIAPI AIErr（\* 	SetArtBounds）（AIArtHandle art）
 	更新Art对象的封闭矩形（边界框）的缓存信息。 
- AIAPI AIErr（\* 	GetArtCenterPointVisible）（AIArtHandle art，AIBoolean \*visible）
 	检查Art品的中心点是否可见。 
- AIAPI AIErr（\* 	SetArtCenterPointVisible）（AIArtHandle art，AIBoolean visible）
 	选择对象时，可以使Art品的中心点可见或不可见。 
- AIAPI AIErr（\* 	GetArtTransformBounds）（AIArtHandle art，AIRealMatrix \* transform，ai :: int32 flags，AIRealRect \* bounds）
 	应用变换矩阵后，获得Art对象的几何边界。 
- AIAPI AIErr（\* 	UpdateArtworkLink）（AIArtHandle art，AIBoolean force，AIBoolean \*updated）
 	检查给定容器的子树中包含的任何链接对象（链接图像或放置对象）是否需要更新，并在需要时更新它们。 
- AIAPI AIBoolean（\* 	ValidArt）（AIArtHandle art，AIBoolean searchAllLayerLists）
 	如果Art对象引用有效，则返回true。 
- AIAPI AIErr（\* 	GetArtOrder）（AIArtHandle art1，AIArtHandle art2，short \* order）
 	获取两个Art品的绘画顺序或遏制关系。 
- AIAPI AIErr（\* 	SelectNamedArtOfLayer）（AILayerHandle图层，const ai :: UnicodeString＆name，AIBoolean matchWholeWord，AIBoolean caseSensitive）
 	通过将字符串与Art对象注释的内容进行匹配来选择图层的指定图稿。 
- AIAPI AIErr（\* 	GetArtRotatedBounds）（AIArtHandle art，AIReal angle，ai :: int32 flags，AIRealRect \* bounds）
 	旋转后获得Art对象的包围矩形。 
- AIAPI AIBoolean（\* 	ArtHasFill）（AIArtHandle art）
 	如果Art对象或后代对象绘制应被视为填充的内容，则返回true。 
- AIAPI AIBoolean（\* 	ArtHasStroke）（AIArtHandle art）
 	如果Art对象或后代对象绘制应被视为笔划的内容，则返回true。 
- AIAPI AIBoolean（\* 	ArtsHaveEqualPaths）（AIArtHandle art1，AIArtHandle art2）
 	如果两个Art对象除了组，路径和复合路径之外只包含其他内容，并且它们具有相同的几何（控制点），则返回true。 
- AIAPI AIErr（\* 	ArtCopyFillStyleIfEqualPaths）（AIArtHandle dstArt，AIArtHandle srcArt）
 	未在AI11及更高版本中实施; 回报kNotImplementedErr。 
- AIAPI AIErr（\* 	ArtCopyStrokeStyleIfEqualPaths）（AIArtHandle dstArt，AIArtHandle srcArt）
 	未在AI11及更高版本中实施; 回报kNotImplementedErr。 
- AIAPI AIErr（\* 	GetInsertionPoint）（AIArtHandle \* art，short \* paintorder，AIBoolean \* editable）
 	获得文档的插入点。 
- AIAPI AIErr（\* 	SetInsertionPoint）（AIArtHandle art）
 	设置文档的插入点。 
- AIAPI AIErr（\* 	GetKeyArt）（AIArtHandle \*Art）
 	获得对象对齐的关键对象。 
- AIAPI AIErr（\* 	CancelKeyArt）（无效）
 	清除对象对齐的关键对象。 
- AIAPI AIErr（\* 	GetDictionary）（AIArtHandle art，struct _AIDictionary \*\* dictionary）
 	获得与Art对象关联的字典。 
- AIAPI AIErr（\* 	SetArtName）（AIArtHandle art，const ai :: UnicodeString＆name）
 	设置Art对象的名称。 
- AIAPI AIErr（\* 	GetArtName）（AIArtHandle art，ai :: UnicodeString＆name，ASBoolean \* isDefaultName）
 	获得Art对象的名称。 
- AIAPI AIErr（\* 	IsArtLayerGroup）（AIArtHandle art，ASBoolean \* isLayerGroup）
 	报告Art对象是否是与图层或子图层对应的组。 
- AIAPI AIErr（\* 	ReleaseToLayers）（常量AIArtHandle技术中，ASBoolean版本）
 	将图层，组或插件组的元素释放到单独的图层。 
- AIAPI AIErr（\* 	ModifyTargetedArtSet）（AIArtHandle \* list，ai :: int32 count，ai :: int32 action）
 	使用指定的操作修改文档中的目标对象集。 
- AIAPI AIBoolean（\* 	IsArtStyledArt）（AIArtHandle art）
 	如果art是另一个对象的样式Art的一部分，则返回true。 
- AIAPI AIBoolean（\* 	IsArtClipping）（AIArtHandle art）
 	如果art添加到剪辑，则返回true 。 
- AIAPI AIErr（\* 	TransferAttributes）（AIArtHandle srcart，AIArtHandle dstart，ai :: uint32 which）
 	将源Art对象的属性传输到目标Art对象。 
- AIAPI AIErr（\* 	GetArtLastChild）（AIArtHandle art，AIArtHandle \*child）
 	获得容器Art对象的最后一个子项。 
- AIAPI AIErr（\* 	SetArtTextWrapProperty）（AIArtHandle art，AIReal offset，AIBoolean invert）
 	设置影响文本环绕文本换行对象的方式的属性; 也就是说，kArtIsTextWrap设置属性的那个。 
- AIAPI AIErr（\* 	GetArtTextWrapProperty）（AIArtHandle art，AIReal \* offset，AIBoolean \* invert）
 	获得影响文本环绕文本换行对象的方式的属性; 也就是说，kArtIsTextWrap设置属性的那个。 
- AIAPI AIErr（\* 	CreateCopyScope）（枚举AICopyScopeKind类，AICopyScopeHandle \*范围）
 	创建并设置复制范围。 
- AIAPI AIErr（\* 	DestroyCopyScope）（AICopyScopeHandle范围）
 	取消并销毁复制范围。 
- AIAPI AIErr（\* 	InsertionPointBadForArtType）（ai :: int16 paintOrder，AIArtHandle prep，ai :: int16 artType）
 	检查在给定插入点处创建或插入给定类型的Art对象是否可以。 
- AIAPI AIErr（\* 	PreinsertionFlightCheck）（AIArtHandle candidateArt，ai :: int16 paintOrder，AIArtHandle prep）
 	根据插入上下文和候选Art的属性，检查是否可以在指定的插入点处插入Art对象或其副本，而无需实际尝试插入。 
- AIAPI AIErr（\* 	SetNote）（AIArtHandle art，const ai :: UnicodeString＆inNote）
 	设置Art对象的音符属性，该属性通常包含用户输入的文本。 
- AIAPI AIErr（\* 	GetNote）（AIArtHandle art，ai :: UnicodeString＆outNote）
 	获得Art对象的注释属性文本，该文本通常包含用户输入的文本。 
- AIAPI AIBoolean（\* 	HasNote）（AIArtHandle art）
 	检查Art品是否附有说明。 
- AIAPI无效（\* 	删除注意）（AIArtHandle art）
 	删除附加到Art对象的注释（如果有）。 
- AIAPI AIErr（\* 	GetArtXMPSize）（AIArtHandle art，size_t \* size）
 	获取与Art对象关联的XMP元数据的大小。 
- AIAPI AIErr（\* 	GetArtXMP）（AIArtHandle art，char \* xmp，size_t size）
 	获得与Art对象的XMP元数据关联的XML数据包。 
- AIAPI AIErr（\* 	SetArtXMP）（AIArtHandle art，const char \* xmp）
 	设置Art对象的XMP元数据，替换任何现有数据。 
- AIAPI AIErr（\* 	GetPreciseArtTransformBounds）（AIArtHandle art，AIRealMatrix \* transform，ai :: int32 flags，AIDoubleRect \* bounds）
 	应用变换矩阵后，获得Art对象的几何边界。 
- AIAPI AIErr（\* 	UncheckedDisposeArt）（AIArtHandle art）
 	从Art树中删除Art对象，对输入的有效性进行最低限度检查，以便最大限度地提高性能。 
- AIAPI AIErr（\* 	ArtIsGraph）（AIArtHandle art，AIBoolean \* artisgraph）
 	报告Art对象是否为图形对象类型。 
- AIAPI AIErr（\* 	SetKeyArt）（AIArtHandle art）
 	将Art设置为对象对齐的KeyArt。关键对象是其他对象对齐的对象。 
- AIAPI AIErr（\* 	GetDrawingMode）（ai :: int32 \* mode）
 	获得当前文档的绘图模式。 
- AIAPI AIErr（\* 	SetDrawingMode）（ai :: int32模式）
 	设置当前文档的绘图模式。 
- AIAPI AIErr（\* 	GetInsertionPointForDrawingMode）（ai :: int32模式，AIArtHandle \* art，short \* paintorder，AIBoolean \* editable）
 	根据绘图模式获得当前文档的插入点。 
- AIAPI AIErr（\* 	GetInsertionPointForCurrentDrawingMode）（AIArtHandle \* art，short \* paintorder，AIBoolean \* editable）
 	根据当前绘图模式获得当前文档的插入点。 
- AIAPI AIErr（\* 	GetPathPolarity）（AIArtHandle art，ai :: int32 \* polarity）
 	获得Art对象的路径极性。 
- AIAPI AIBoolean（\* 	IsPixelPerfect）（AIArtHandle art）
 	报告Art品是否处于像素完美模式。 
- AIAPI AIErr（\* 	SetPixelPerfect）（AIArtHandle art，AIBoolean isPixelPerfect）
 	为Art品打开或关闭像素完美模式。 
- AIAPI AIBoolean（\* 	ObjectsAreEquivalent）（AIArtHandle art1，AIArtHandle art2）
 	比较两个Art对象以确定它们在几何，外观属性和字典内容中是否等效。通常，一个对象在当前文档中，另一个在另一个文档中。 
- AIAPI AIErr（\* 	IsArtALayerInSymbol）（AIArtHandle art，AIBoolean \* isLayerInSymbol）
 	报告Art对象是否充当符号模式中的图层。 
- AIAPI AIErr（\* 	GetArtTimeStamp）（AIArtHandle art，size_t \* timeStamp）
 	获得Art对象的修改时间戳。 
## paintOrder 枚举常量
```
typedef enum AIPaintOrder {
	/* Default value */
	kPlaceDefault = 0,
	/** Place above the prepositional object */
	kPlaceAbove = 1,
	/** Place below the prepositional object */
	kPlaceBelow,
	/** Place within and at the top of the prepositional container */
	kPlaceInsideOnTop,
	/** Place within and at the bottom of the prepositional container */
	kPlaceInsideOnBottom,
	/** Place at the top of the paint order, ignoring prepositional object */
	kPlaceAboveAll,
	/** Place at the bottom of the paint order, ignoring prepositional object */
	kPlaceBelowAll
} AIPaintOrder;
```
## AIArtHandle
```
typedef struct ArtObject* AIArtHandle
Opaque reference to an art object.

Access using AIArtSuite.
```
## 使用实例
```
AIArtHandle artGroup = NULL;
sAIArt->GetFirstArtOfLayer(PolygonLayer, &artGroup);
AIArtHandle art = NULL;
sAIArt->NewArt(kPathArt, kPlaceInsideOnTop, artGroup, &art);
//setNote
sAIArt->SetNote("该属性通常包含用户输入的文本。需要记录保存的信息，一般，号分割的字符串，不过插件一般使用Dictionary");
//HasNote,GetNote
if (sAIArt->HasNote(artGroup)){
		ai::UnicodeString infoNote;
		sAIArt->GetNote(artGroup, infoNote);
		}
//DisposeArt
sAIArt->DisposeArt(artGroup);

//设置隐藏图层内容不可见
	AILayerHandle FirstLayer;
	AILayerHandle NextLayer = NULL;

	sAILayer->GetLayerFirstChild(m_hideLayer,&FirstLayer);
	while(FirstLayer)
	{
		AIArtHandle ArtGroup;
		AIArtHandle Art;
		sAIArt->GetFirstArtOfLayer(FirstLayer,&ArtGroup);
		sAIArt->GetArtFirstChild(ArtGroup,&Art);
		
		AILayerHandle LayerTemp;
		LayerTemp =  FirstLayer;
		sAILayer->GetNextLayer(FirstLayer,&FirstLayer);
		if(!Art)
			sAILayer->DeleteLayer(LayerTemp);
	}

	sAILayer->SetLayerVisible(m_hideLayer, false);
	
	AIRealRect artRect;
   sAIArt->GetArtBounds(m_pointArt, &artRect);
   sAIMathSuite->AIRealMatrixSetTranslate(&maxtrix, ListIter->h - (artRect.right + artRect.left) / 2, ListIter->v - (artRect.top + artRect.bottom) / 2);
   sAITransformArt->TransformArt(m_pointArt, &maxtrix, 1, kTransformObjects);
   sAIArt->SetArtName(m_pointArt, ai::UnicodeString("XXXX"));
   
   AIArtHandle textPathPoly = NULL;
   AIArtHandle textFrame;//已存在变量
   sAIArt->DuplicateArt(textFrame, kPlaceBelow,textFrame, &textPathPoly);
   AIArtHandle textFrameBackgroud;//已存在变量
   sAIArt->ReorderArt(textFrameBackgroud, kPlaceInsideOnTop, ArtGroup);
   
   AIArtHandle outlines=NULL;
   	sAITextFrame->CreateOutline(textFrame, &outlines);
   
   	AIArtHandle outlinef=NULL,outlinen=NULL;
   	sAIArt->GetArtFirstChild(outlines, &outlinef);
   
   	//描边属性
   	AIPathStyle pathstyle;
   	pathstyle.fillPaint = false;
   	pathstyle.strokePaint = true;
   	AIStrokeStyle strokestyle;
   	strokestyle.width = this->borderWidth;
   	strokestyle.cap = kAIRoundCap;
   	strokestyle.join = kAIRoundJoin;
   	strokestyle.color = borderColor;
   	pathstyle.stroke = strokestyle;
   
   	sAIPathStyle->SetPathStyle(outlinef, &pathstyle);
   
   	sAIArt->GetArtSibling(outlinef, &outlinen); 
   
   	while (outlinen)
   	{
   		sAIPathStyle->SetPathStyle(outlinen, &pathstyle);
   		sAIArt->GetArtSibling(outlinen, &outlinen);
   	}
   
   	sAIArt->DisposeArt(textFrame);
   	//使用GetArtPriorSibling
   	// get the previous art
    error = sArt->GetArtPriorSibling(art, &art);
    if (error) return error;
    if (!art){
            // art was first in group
            // see if parent has previous art
            error = sArt->GetArtParent(art, &art);
            if (error) return error;
            ...
   //使用GetArtParent  ,GetArtSibling
     ...
     // see if parent has sibling
     error = sArt->GetArtParent(nextart, &nextart);
     if (error) return error;
     error = sArt->GetArtSibling(nextart, &nextart);
     if (error) return error;
     if (nextart) {    // parent has sibling
     ...
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
