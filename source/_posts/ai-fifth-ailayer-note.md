---
title: AI组件开发（五）--AILayerSuite
date: 2019-03-24 21:09:15
tags: 
    - AI  
categories: AI组件开发
---
文档中的图稿包含在图层中。AILayerSuite有一堆图层列表。堆栈中最顶层的层是当前正在编辑的层
<!--more-->
# AILayerSuite
每个图层都包含一组对象，可以包含子图层。所有文档都至少以一个图层开头，用户或插件可以向其添加任意数量的新图层。图层是图稿树的容器; 用于AIArtSuite::GetFirstArtOfLayer()获取属于图层的Art对象组。

除了包含图稿之外，图层还有一个名称，一种颜色（用于表示选定的art），以及指示其是可见，可编辑还是已锁定，可打印以及是否将放置的Art显示为灰色的状态标志。图层引用是不透明的; 使用此套件中的函数来获取和设置图层属性。

多个图层属性会影响图层上的图稿是否可编辑。通常，不能选择不可编辑图层上的对象，因此无需检查对当前选择进行操作的插件。但是，在迭代图层或图稿时，请GetLayerEditable()在尝试对图稿进行任何更改之前调用每个图层。

可以将图层指定为模板图层，其中包含用于指导文档图稿构造的模板图稿。例如，模板可能包含正在跟踪的图像。永远不会打印模板图层或将其导出为最终格式的文件格式。

有四个与图层关联的通知程序： 
kAICurrentLayerNotifier 
kAILayerDeletionNotifier 
kAILayerSetNotifier 
kAILayerOptionsNotifier

使用SPBasicSuite::AcquireSuite()常量kAILayerSuite和使用获取此套件kAILayerVersion。
## 注册声明
```
//xxxSuites.h
#include <AILayer.h>
extern  "C" AILayerSuite\*                sAILayer;

//xxxSuites.cpp
extern "C"{
AILayerSuite\*               sAILayer = NULL;
}

ImportSuite gImportSuites[] = {
    IMPORT_TEXT_SUITES
    kAILayerSuite, kAILayerSuiteVersion, &sAILayer,
        nil, 0, nil
      };

```
## 方法
- AIAPI AIErr（\* 	CountLayers）（ai :: int32 \* count）
 	获取当前文档图层列表中的顶级图层数。 
- AIAPI AIErr（\* 	GetNthLayer）（ai :: int32 n，AILayerHandle \*layer）
 	从当前文档中按索引获得顶级图层。 
- AIAPI AIErr（\* 	GetCurrentLayer）（AILayerHandle \*layer）
 	获得当前文档中当前图层的引用。 
- AIAPI AIErr（\* 	SetCurrentLayer）（AILayerHandle layer）
 	使图层最新。 
- AIAPI AIErr（\* 	GetFirstLayer）（AILayerHandle \* first）
 	获得当前文档图层列表的最顶层。 
- AIAPI AIErr（\* 	GetNextLayer）（AILayerHandle prev，AILayerHandle \* next）
 	在堆叠顺序中获得给定图层下方的下一层，位于图层列表堆栈的同一级别。 
- AIAPI AIErr（\* 	InsertLayer）（AILayerHandle layer，ai :: int16 paintOrder，AILayerHandle \* newLayer）
 	向文档添加新图层，将其插入到相对于现有图层的绘制顺序的图层列表中。 
- AIAPI AIErr（\* 	DeleteLayer）（AILayerHandle layer）
 	从当前文档中删除图层及其包含的所有图稿。 
- AIAPI AIErr（\* 	GetLayerTitle）（AILayerHandle layer，ai :: UnicodeString＆title）
 	获得图层的名称。 
- AIAPI AIErr（\* 	SetLayerTitle）（AILayerHandle layer，const ai :: UnicodeString＆newTitle）
 	设置图层的名称。 
- AIAPI AIErr（\* 	GetLayerColor）（AILayerHandle layer，AIRGBColor \*color）
 	获得在图层中勾画选定图稿时使用的颜色。 
- AIAPI AIErr（\* 	SetLayerColor）（AILayerHandle layer，AIRGBColor color）
 	设置在图层中勾画选定图稿时使用的颜色。 
- AIAPI AIErr（\* 	GetLayerVisible）（AILayerHandle layer，AIBoolean \*visible）
 	报告图层的可见性状态，这会影响是否绘制图稿。 
- AIAPI AIErr（\* 	SetLayerVisible）（AILayerHandle layer，AIBoolean visible）
 	设置图层的可见性状态，这会影响是否绘制图稿。 
- AIAPI AIErr（\* 	GetLayerPreview）（AILayerHandle layer，AIBoolean \*preview）
 	报告图层是指定预览模式还是轮廓模式。 
- AIAPI AIErr（\* 	SetLayerPreview）（AILayerHandle layer，AIBoolean preview）
 	将图层设置为预览模式或轮廓模式。 
- AIAPI AIErr（\* 	GetLayerEditable）（AILayerHandle layer，AIBoolean \*editable）
 	报告图层是可编辑还是已锁定。 
- AIAPI AIErr（\* 	SetLayerEditable）（AILayerHandle layer，AIBoolean editable）
 	设置图层是可编辑还是锁定。 
- AIAPI AIErr（\* 	GetLayerPrinted）（AILayerHandle layer，AIBoolean \*printed）
 	报告在打印文档时是否将此图层视为可打印。 
- AIAPI AIErr（\* 	SetLayerPrinted）（AILayerHandle layer，AIBoolean printed）
 	设置打印文档时是否将此图层视为可打印。 
- AIAPI AIErr（\* 	GetLayerDimPlacedImages）（AILayerHandle layer，AIBoolean \* dimmed）
 	报告渲染时图层中的图像是否变暗（颜色向白色移动）。 
- AIAPI AIErr（\* 	SetLayerDimPlacedImages）（AILayerHandle layer，AIBoolean dimmed）
 	设置渲染时图层中的图像是否变暗（颜色向白色移动）。 
- AIAPI AIErr（\* 	GetLayerSelected）（AILayerHandle layer，AIBoolean \*selected）
 	报告是否选择了图层。 
- AIAPI AIErr（\* 	SetLayerSelected）（AILayerHandle layer，AIBoolean selected）
 	设置是否选择图层。 
- AIAPI AIErr（\* 	GetLayerByTitle）（AILayerHandle \*layer，const ai :: UnicodeString＆title）
 	按标题获得图层。 
- AIAPI AIErr（\* 	LayerHasArt）（AILayerHandle layer，AIBoolean \* hasArt）
 	报告图层是否包含子图层以外的对象。 
- AIAPI AIErr（\* 	LayerHasSelectedArt）（AILayerHandle layer，AIBoolean \* hasSel）
 	报告图层是否包含任何选定的图稿。 
- AIAPI AIErr（\* 	取消选择ArtOnLayer）（AILayerHandle layer）
 	取消选择图层上的所有对象。 
- AIAPI AIErr（\* 	SelectArtOnLayer）（AILayerHandle layer）
 	选择可以选择的图层上的所有对象。 
- AIAPI AIErr（\* 	GetLayerIsTemplate）（AILayerHandle layer，AIBoolean \* isTemplate）
 	报告图层是否为模板图层。 
- AIAPI AIErr（\* 	SetLayerIsTemplate）（AILayerHandle layer，AIBoolean isTemplate）
 	设置图层是否为模板图层。 
- AIAPI AIErr（\* 	GetPrevLayer）（AILayerHandle接下来，AILayerHandle \*prev）
 	在堆叠顺序中获得给定图层上方的图层。 
- AIAPI AIErr（\* 	GetLayerDimmedPercent）（AILayerHandle layer，ai :: int32 \*percent）
 	当图层设置为灰色时，获得用于绘制图像的调光因子。 
- AIAPI AIErr（\* 	SetLayerDimmedPercent）（AILayerHandle layer，ai :: int32 percent）
 	设置图层设置为灰色时用于绘制图像的调光系数。 
- AIAPI AIErr（\* 	GetLayerFirstChild）（const AILayerHandle layer，AILayerHandle \*child）
 	获得图层的第一个子子图层。 
- AIAPI AIErr（\* 	GetLayerParent）（const AILayerHandle layer，AILayerHandle \* parent）
 	获得图层的父级。 
- AIAPI AIErr（\* 	InsertLayerAtArt）（const AIArtHandle art，AIPaintOrder paintOrder，AILayerHandle \* newLayer）
 	在相对于艺术对象的绘制顺序位置处将新图层插入到文档中。 
- AIAPI AIErr（\* 	ChangeLayerToGroup）（const AILayerHandle layer，const AIArtHandle group）
 	将此图层的子图层转换为简单组。 
- AIAPI AIErr（\* 	GetNextPreorderLayer）（AILayerHandle prev，AILayerHandle \* next）
 	在前序遍历中获得下一层，首先查找后代，然后是兄弟姐妹。 
- AIAPI AIErr（\* 	GetNextNonChildPreorderLayer）（AILayerHandle prev，AILayerHandle \* next）
 	在前序遍历中获得下一层，跳过后代并仅返回兄弟层。 
## paintOrder 枚举常量
```
typedef enum AIPaintOrder {
	/** Default value */
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
## AILayerHandle
```
typedef struct _t_AILayerOpaque* AILayerHandle
Opaque reference to a layer.
```
## 使用实例
```
//使用InsertLayer，SetLayerTitle，GetLayerByTitle，DeleteLayer
AILayerHandle m_pointLinePolyLayer;
AILayerHandle m_borderMarkLayer;
sAILayer->InsertLayer(m_pointLinePolyLayer, kPlaceInsideOnTop, &m_borderMarkLayer);//在点线面图层上添加色带图层
sAILayer->SetLayerTitle(m_borderMarkLayer, ai::UnicodeString("色带图层"));//设置新添加图层名称
AILayerHandle polyAnnoLayers;
sAILayer->GetLayerByTitle(&polyAnnoLayers, ai::UnicodeString(L"面注记图层"));//找到名为面注记图层的layer
sAILayer->DeleteLayer(polyAnnoLayers);//删除面注记图层layer

//使用GetLayerFirstChild，GetNextLayer
AILayerHandle m_hideLayer;
AILayerHandle FirstLayer;
AILayerHandle NextLayer = NULL;
sAILayer->GetLayerFirstChild(m_hideLayer,&FirstLayer);
//遍历如果存在图稿，则删除该图层
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
	
//使用GetNthLayer获得最上面的图层
AILayerHandle hideLayer = NULL;
    sAILayer->GetNthLayer(0, &hideLayer);
    AILayerHandle childHideLayer = NULL;
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
