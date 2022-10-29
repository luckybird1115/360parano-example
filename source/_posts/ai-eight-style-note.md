---
title: AI组件开发（八）--AIArtStyleSuite
date: 2019-03-25 09:46:16
tags:
categories:
---
Art Style套件中的功能用于访问和修改Illustrator文档中图稿的art样式
<!--more-->
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [AIArtStyleSuite](#aiartstylesuite)
  - [方法](#%E6%96%B9%E6%B3%95)
- [相关类，结构体，指针](#%E7%9B%B8%E5%85%B3%E7%B1%BB%E7%BB%93%E6%9E%84%E4%BD%93%E6%8C%87%E9%92%88)
  - [AIArtHandle](#aiarthandle)
  - [AIArtStyleHandle](#aiartstylehandle)
  - [AIPathStyle](#aipathstyle)
  - [AIStrokeStyle](#aistrokestyle)
  - [AIFillStyle](#aifillstyle)
- [使用实例](#%E4%BD%BF%E7%94%A8%E5%AE%9E%E4%BE%8B)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
# AIArtStyleSuite
Art Style套件中的功能用于访问和修改Illustrator文档中图稿的art样式
在AI8中，Illustrator引入了插件组的概念，其中实际打印的art（结果art）与用户编辑的art（编辑art）分开。随着art风格的引入，这一概念在AI9中得到了扩展。现在，文档中的任何art对象（包括组和文本对象）都可以选择附加“样式art”。正是这种“风格art”得到了印刷，而不是原始的“源art”。

应用于art的art风格控制着风格art的生成方式。插件不应该直接修改样式art本身，可以随时重新生成，但应该使用art风格。

导出插件应导出应用于对象的样式化art（如果有），而不是原始源art。

在UI中，应用于Illustratorart对象的art风格称为对象的外观，并通过“外观”调板进行操作。外观也可以通过“图形样式”调板保存并在其他对象上重复使用。

使用SPBasicSuite::AcquireSuite()常量kAIArtStyleSuite和使用获取此套件kAIArtStyleVersion。
## 方法
- AIAPI AIErr（\* 	GetStyledArt）（AIArtHandle art，AIArtHandle \* styledArt）
 	获得art对象的样式。 
 	返回 styleArt [out\]用于返回样式化art对象的缓冲区，如果没有应用特殊样式，则返回原始art对象。NULL出错时返回; 例如，如果由于内存限制而无法执行动态效果。
- AIAPI AIErr（\* 	GetArtStyle）（AIArtHandle art，AIArtStyleHandle \* artStyle）
 	获得与art对象关联的art风格。 
- AIAPI AIErr（\* 	GetCurrentArtStyle）（AIArtStyleHandle \* artStyle，AIBoolean \* mixedState）
 	获得“外观”调板中当前显示的art样式。 
 	artStyle 	[out\]用于返回art风格参考的缓冲区。
    mixedState 	[out\]如果Appearance调色板显示“Mixed”，则返回true的缓冲区。在这种情况下，返回的art风格是一种简单的风格，它试图收集附加到当前文档中当前所选对象的art风格的公共属性。
- AIAPI AIErr（\* 	GetArtStyleByName）（AIArtStyleHandle \* artStyle，const ai :: UnicodeString＆name，AIBoolean searchAllStyles）
 	从当前文档中按唯一名称获得art样式。 
- AIAPI AIErr（\* 	CountNamedArtStyles）（ai :: int32 \* count）
 	获取“图形样式”调板中当前列出的art样式的数量。 
- AIAPI AIErr（\* 	GetNthNamedArtStyle）（ai :: int32 n，AIArtStyleHandle \* artStyle）
 	按索引获得命名的art样式。 
- AIAPI AIErr（\* 	SetArtStyle）（AIArtHandle art，AIArtStyleHandle artStyle）
 	将art样式应用于ar品。 
- AIAPI AIErr（\* 	SetCurrentArtStyle）（AIArtStyleHandle artStyle）
 	将art样式应用于画板上当前目标对象。 
- AIAPI AIErr（\* 	GetArtStyleName）（AIArtStyleHandle artStyle，ai :: UnicodeString＆name，AIBoolean \* isAnonymous）
 	获得art样式的唯一名称。 
- AIAPI AIErr（\* 	SetArtStyleName）（AIArtStyleHandle artStyle，const ai :: UnicodeString＆name）
 	设置命名art样式的唯一名称; 也就是说，在“图形样式”调板中显示的那个。 
- AIAPI AIErr（\* 	GetPaintAttributes）（AIArtStyleHandle artStyle，AIPathStyle \* aiPathStyle，AIArtStylePaintData \* paintData）
 	获得art样式的绘画信息。 
- AIAPI AIErr（\* 	NewStyle）（AIPathStyle \* aiPathStyle，AIArtStylePaintData \* paintData，AIDictionaryRef blendDict，AIArtStyleHandle \* newStyle）
 	创造一种新的art风格。 
- AIAPI AIErr（\* 	AddNamedStyle）（AIArtStyleHandle artStyle，const ai :: UnicodeString＆name，AIBoolean uniquify，AIArtStyleHandle \* namedStyle）
 	将匿名样式转换为命名样式，该样式显示在“图形样式”调板中。 
- AIAPI AIErr（\* 	RemoveNamedStyle）（AIArtStyleHandle namedStyle，AIArtStyleHandle \* anonStyle）
 	将命名样式（在“图形样式”调板中显示）转换为匿名样式，
- AIAPI AIErr（\* 	CreateAnonymousStyle）（AIArtStyleHandle namedStyle，AIArtStyleHandle \* anonStyle）
 	创建命名art样式的匿名副本（显示在“图形样式”调板中）。 
- AIAPI AIErr（\* 	RedefineNamedStyle）（AIArtStyleHandle namedStyle，AIArtStyleHandle dstStyle）
 	重新定义命名的art样式以具有与另一样式相同的特征。 
- AIAPI AIErr（\* 	MoveNamedStyle）（AIArtStyleHandle namedStyle，ai :: int32 index）
 	将命名的art样式移动到“图形样式”调板中的位置。 
- AIAPI AIErr（\* 	GetArtStyleByNameFromDocument）（AIArtStyleHandle \* artStyle，const ai :: UnicodeString＆name，AIDocumentHandle document）
 	通过指定文档中的唯一名称获得命名的art样式。 
- AIAPI AIErr（\* 	CountNamedArtStylesFromDocument）（ai :: int32 \* count，AIDocumentHandle document）
 	获取指定文档中命名的art样式的数量（当该文档是最新的时，在“图形样式”调板中显示的样式）。 
- AIAPI AIErr（\* 	GetNthNamedArtStyleFromDocument）（ai :: int32 n，AIArtStyleHandle \* artStyle，AIDocumentHandle document）
 	按索引获得指定文档的命名art样式。 
- AIAPI AIErr（\* 	当量）（AIArtStyleHandle artStyle1，AIArtStyleHandle artStyle2，AIBoolean \*结果）
 	测试两种art风格之间的等价性。 
- AIAPI AIErr（\* 	SortNamedStyles）（void）
 	按名称按字母顺序对当前文档中的命名art样式进行排序。 
- AIAPI AIErr（\* 	GetDefaultArtStyle）（AIArtStyleHandle \* artStyle）
 	获得当前文档的默认art样式，该样式始终首先列在“图形样式”调板中。 
- AIAPI AIErr（\* 	GetDefaultArtStyleName）（ai :: UnicodeString＆name）
 	获得当前文档的默认art样式的名称，该名称始终首先列在“图形样式”调板中。 
- AIAPI AIBoolean（\* 	ValidateArtStyle）（AIArtStyleHandle artStyle）
 	报告当前文档中是否存在artstyle。 
- AIAPI AIErr（\* 	FlattenStyle）（AIArtHandle art）
 	扩展art的风格，将art品替换为可返回的GetStyledArt()。 
- AIAPI AIErr（\* 	GetArtStyleType）（AIArtStyleHandle artStyle，ai :: int16 \* type）
 	获得art风格的类型。 
- AIAPI AIBoolean（\* 	CanExecute）（AIArtStyleHandle artStyle）
 	报告art风格是否包含执行所需的所有部分。 
- AIAPI AIReal（\* 	GetArtStyleScaleFactor）（AIArtHandle art）  
 	返回art对象样式的比例因子。  
 	“缩放笔触和效果”选项允许缩放笔画粗细和附加到art对象的效果。缩放因子与art对象一起存储，并且在应用样式后应用。例如，如果附加到对象的样式具有权重为2 pt的笔划，并且对象的比例因子为2.5，则视觉笔触权重为5 pt。                  
   注意：  
   GetPaintAttributes()获取未缩放的参数值，以及来自的函数AIArtStyleParserSuite。  
   但是，AIPathStyleSuite调用返回附加到对象的缩放笔画粗细。  
   参数：art     
   返回：  
   比例因子。  
 	 
- AIAPI AIErr（\* 	SetArtStyleScaleFactor）（AIArtHandle art，AIReal scaleFactor）
 	设置art对象样式的比例因子。 
- AIAPI AIErr（\* 	ResetArtStyleScaleFactor）（AIArtHandle art）
 	将新样式应用于按对象的当前比例因子缩放的art对象，并将对象的比例因子重置为1。 
- AIAPI AIErr（\* 	TransformObjectArtStyle）（AIArtHandle art，AIRealMatrix \* matrix，AIReal lineScale，ai :: int32 flags）
 	改变对象的art风格。 
 	参数：
 	art 需要变换的art对象
    matrix 	指向转换矩阵的指针。
    lineScale 	要应用于art对象当前样式中指定的线条绘制样式值的比例因子。
    flags 	控制转换执行方式的位标志。AITransformArtOptions值的逻辑OR 。如果kTransformObjects标志打开，则假设art对象本身也已经被相同的矩阵变换，并且已经执行了点，后代art等的任何变换。这允许立即调整梯度向量和依赖于对象边界的其他样式属性，而不是延迟到art同步。
- AIAPI AIArtStyleHasAttrs（\* 	ExamineStyle）（AIArtStyleHandle artStyle，AIArtStyleHasAttrs checkFor）
 	检查特定属性的art风格，并返回找到的属性。 
- AIAPI AIErr（\* 	GetToolArtStyle）（AIArtStyleHandle \* artStyle）
 	根据应用程序首选项获得将用于工具创建的新art的artstyle。 
- AIAPI AIErr（\* 	GetFlattenedArt）（AIArtHandle art，AIArtHandle \* flattenedArt）
 	扩展ar的风格，将art替换为可返回的art GetStyledArt()。 
- AIAPI AIBoolean（\* 	HasEffectiveNullStyle）（AIArtHandle art）
 	报告art对象是否具有等效于null样式的样式。 
- AIAPI AIErr（\* 	GetPaintMap）（AIArtStyleHandle artStyle，AIPathStyleMap \* aiPathStyleMap）
 	获得art样式的路径样式的地图，如下所示GetPaintAttributes()。 
 	
# 相关类，结构体，指针
## AIArtHandle
```
typedef struct ArtObject* AIArtHandle
Opaque reference to an art object.
Access using AIArtSuite.
```
## AIArtStyleHandle
```
typedef struct _t_AIArtStyle* AIArtStyleHandle
A reference to an art style.
See also:
AIArtStyleSuite.
```
## AIPathStyle
描述如何绘制路径的填充和描边。

完整样式完全指定绘画信息。  
一个部分款式可以指定的绘画信息的子集。例如，它可用于表示选择的常见绘制属性，或修改属性的子集而不影响其他属性。在这种情况下，路径样式与样式映射配对，AIPathStyleMap指定具有有效值的属性子集。  
注意不要将部分样式传递给需要完整样式的函数; 未指定的字段可以包含无效值。  
- AIBoolean 	fillPaint  
 	Whether or not to fill the path.   
- AIBoolean 	strokePaint  
 	Whether or not to stroke the path.   
- struct AIFillStyle 	fill  
 	Fill style, if fillPaint is true.   
- struct AIStrokeStyle 	stroke  
 	Stroke style, if strokePaint is true.    
- AIBoolean 	clip  
- AIBoolean 	lockClip  
- AIBoolean 	evenodd  
 	When true, use the even-odd rule to determine path insideness.   
- AIReal 	resolution  
 	Path's resolution.   
## AIStrokeStyle
- AIColor 	color  
 	Stroke color.   
- AIBoolean 	overprint  
 	Overprint - not meaningful if the color is a pattern.   
- AIReal 	width  
 	Line width in points.   
- struct AIDashStyle 	dash  
 	Stroke dash.   
- enum AILineCap 	cap  
 	Line cap.
    ```
    Types of line caps for strokes.
        Enumerator:
        kAIButtCap 	
        Butt line caps.//平头
        
        kAIRoundCap 	
        Round line caps.//圆头
        
        kAIProjectingCap 	
        Projecting line caps.//方头
    ```
- enum AILineJoin 	join  
 	Line join
    ```
    Types of line joins for strokes.
    
    Enumerator:
    kAIMiterJoin 	
    Miter line joins.//斜接连接
    
    kAIRoundJoin 	
    Round line joins.//圆角连接
    ```
- AIReal 	miterLimit  
 	Line miter limit. 
## AIFillStyle
- AIColor 	color
 	Fill color. 
- AIBoolean overprint
 	When true, print any art beneath this, then print the fill of this object over it. 
 	
# 使用实例
```
bool isArtStyle=true;//是否使用图形样式库设置样式
if (isArtStyle){
    AIArtStyleHandle artStyle=NULL;
    sAIArtStyle->GetArtStyleByName(&artStyle, ai::UnicodeString(artStyleName), 1);
    if (!artStyle){
        sAIArt->DisposeArt(art);
        return -1;
    }
    sAIArtStyle->SetArtStyle(art, artStyle);
}
else{
     AIColor color;
	AIColor colorFill;
	float CMYK[4] = {23,211,22,56};
	float CMYKFill[4] = {23,223,12,53};
   // 描边颜色
	color.kind = kFourColor;
	color.c.f.cyan = CMYK[0];
	color.c.f.magenta = CMYK[1];
	color.c.f.yellow = CMYK[2];
	color.c.f.black = CMYK[3];

	//内部填充颜色
	colorFill.kind = kFourColor;
	colorFill.c.f.cyan = CMYKFill[0];
	colorFill.c.f.magenta = CMYKFill[1];
	colorFill.c.f.yellow = CMYKFill[2];
	colorFill.c.f.black = CMYKFill[3];

	AIPathStyle PathStyle;
	PathStyle.fillPaint = true; // 面内颜色填充 设置为true 否则无法在面内填充颜色
	PathStyle.strokePaint = true;
	PathStyle.stroke.width = 0.5;
	PathStyle.stroke.color = color;
	PathStyle.fill.color = colorFill; // 内部填充
    sAIPathStyle->SetPathStyle(art, &PathStyle);
    //设置透明度
    sAIBlendStyle->SetOpacity(art, 0.4);
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
