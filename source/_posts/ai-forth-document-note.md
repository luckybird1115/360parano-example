---
title:  AI组件开发（四）--AIDocumentSuite
date: 2019-03-24 20:19:52
tags: 
    - AI  
categories: AI组件开发
---
获取和设置有关当前文档的信息。
<!--more-->
# AIDocumentSuite
使用这些函数可以获取和设置有关当前文档的信息。
某些功能仅在读取文件格式时适用。有些是在文件格式写入期间使用，但可以随时使用。
## 函数
- AIAPI AIErr（\* 	GetDocumentFileSpecification）（ai :: FilePath＆file）
 	获得当前文档的文件规范。 
- AIAPI AIErr（\* 	GetDocumentPageOrigin）（AIRealPoint \* origin）
 	获得相对于标尺原点指定的可成像页面左下角的坐标。 
- AIAPI AIErr（\* 	SetDocumentPageOrigin）（AIRealPoint \* origin）
 	设置可成像页面左下角的坐标。 
- AIAPI AIErr（\* 	SetDocumentRulerOrigin）（AIRealPoint \* origin）
 	设置当前文档的标尺原点。 
- AIAPI AIErr（\* 	GetDocumentRulerUnits）（ai :: int16 \*units）
 	获得当前测量单位，显示在标尺中并显示在对话框中。 
- AIAPI AIErr（\* 	SetDocumentRulerUnits）（ai :: int16 units）
 	设置当前测量单位，显示在标尺中并显示在对话框中。 
- AIAPI AIErr（\* 	GetDocumentCropStyle）（AICropMarkStyle \* cropStyle）
 	获得由“创建裁剪标记”命令和过滤器创建的裁剪标记的样式。 
- AIAPI AIErr（\* 	SetDocumentCropStyle）（AICropMarkStyle cropStyle）
 	设置“创建裁剪标记”命令和过滤器创建的裁剪标记的样式。 
- AIAPI AIErr（\* 	GetDocumentSetup）（AIDocumentSetup \* setup）
 	获得有关通过文档“设置”对话框设置的当前文档的信息。 
- AIAPI AIErr（\* 	SetDocumentSetup）（AIDocumentSetup \* setup）
 	设置文档设置信息，通过文档设置对话框设置。 
- AIAPI AIErr（\* 	GetDocumentModified）（AIBoolean \* modified）
 	获得文档自上次保存以来是否已被编辑。 
- AIAPI AIErr（\* 	SetDocumentModified）（AIBoolean修改）
 	设置当前文档的修改状态。 
- AIAPI AIErr（\* 	GetDocumentFileFormat）（AIFileFormatHandle \* fileFormat）
 	获得保存当前文档时要使用的文件格式。 
- AIAPI AIErr（\* 	SetDocumentFileFormat）（AIFileFormatHandle fileFormat）
 	设置保存当前文档时要使用的文件格式。 
- AIAPI AIErr（\* 	GetDocumentFileFormatParameters）（AIDocumentFileFormatParameters \*参数）
 	获得与当前文档的关联文件格式关联的用户参数块。 
- AIAPI AIErr（\* 	SetDocumentFileFormatParameters）（AIDocumentFileFormatParameters参数）
 	设置与当前文档的关联文件格式关联的用户参数块。 
- AIAPI AIErr（\* 	GetDocument）（AIDocumentHandle \*文件）
 	获得当前文档的不透明引用，以便与可以对当前文档以外的文档进行操作的函数一起使用。 
- AIAPI AIErr（\* 	WriteDocument）（const ai :: FilePath＆file，const char \* fileFormatName，AIBoolean askForParms）
 	将当前文档写入文件而不修改文档或更改其修改状态。 
- AIAPI AIErr（\* 	GetDocumentMiPrintRecord）（AIDocumentMiPrintRecordPtr打印）
 	获得当前文档的打印记录。 
- AIAPI AIErr（\* 	GetDocumentRulerOrigin）（AIRealPoint \* origin）
 	获得当前文档的标尺原点。 
- AIAPI AIErr（\* 	UpdateLinks）（AIBoolean \* updatedSomething）
 	如果需要，更新当前文档中的链接对象。 
- AIAPI AIErr（\* 	GetDocumentZoomLimit）（AIReal \* min，AIReal \* max）
 	获得缩放的限制作为比例因子。 
- AIAPI AIErr（\* 	GetDocumentMaxArtboardBounds）（AIRealRect \* bounds）
 	获得任何画板的最大有效范围，相对于当前文档的标尺原点表示，而不管文档画板的当前大小。 
- AIAPI AIErr（\* 	DocumentExists）（AIDocumentHandle文档，AIBoolean \*exists）
 	报告文件是否存在; 也就是说，是一个打开的文档或剪贴板文档。 
- AIAPI AIErr（\* 	GetDictionary）（struct _AIDictionary \*\*Dictionary）
 	获得与当前文档关联的录制Dictionary。 
- AIAPI AIErr（\* 	GetDocumentColorModel）（ai :: int16 \* colorModel）
 	获得文档颜色模型。 
- AIAPI AIErr（\* 	SetDocumentColorModel）（ai :: int16 colorModel）
 	在加载时设置文档的预期颜色模型。 
- AIAPI AIErr（\* 	GetDocumentProfiles）（AIColorProfile \* rgbProfile，AIColorProfile \* cmykProfile，AIColorProfile \* grayProfile）
 	获得当前文档的颜色校准配置文件。 
- AIAPI AIErr（\* 	SetDocumentProfiles）（AIColorProfile rgbProfile，AIColorProfile cmykProfile，AIColorProfile grayProfile）
 	设置当前文档的颜色校准配置文件。 
- AIAPI AIErr（\* 	Copy）（）
 	将当前选定的对象复制到剪贴板。 
- AIAPI AIErr（\* 	Cut）（）
 	将当前选定的对象剪切到剪贴板。 
- AIAPI AIErr（\* 	Paste）（）
 	将剪贴板的内容粘贴到文档中。 
- AIAPI AIErr（* AIDocumentSuite :: SyncDocument）
更新缓存的图稿属性。
例如，组的选择状态被缓存，并根据其子项的状态确定。您通常不需要调用此函数。Illustrator会根据需要重新计算缓存的属性。
- AIAPI AIErr（\* 	GetNonRecordedDictionary）（struct _AIDictionary \*\*Dictionary）
 	获得与当前文档关联的未记录Dictionary。 
- AIAPI int（\* 	GetAIVersion）（）
 	获得上次保存当前文档的Illustrator文件格式的版本。 
- AIAPI AIErr（\* 	DocumentHasTransparency）（AIBoolean \* hasTransparency，AIBoolean detectOverprint）
 	报告是否根据当前视图设置渲染当前文档需要任何非不透明的绘制操作。 
- AIAPI AIErr（\* 	DocumentHasSpotColorArt）（AIBoolean \* hasSpotColorArt）
 	报告是否根据当前视图设置渲染当前文档需要绘制任何专色。 
- AIAPI AIErr（\* 	GetDocumentAssetMgmtInfo）（AIBoolean \* managed，ai :: int32 \* pNAlternates，AIBoolean \* checkedOut，const char \*\* URL，AIBoolean \* canEdit，char \* fileType）
 	获得资产管理信息。 
- AIAPI AIErr（\* 	SetDocumentAssetMgmtInfo）（AIBoolean \* managed，AIBoolean \* checkedOut，const char \* URL，AIBoolean \* canEdit）
 	设置资产管理信息。 
- AIAPI AIErr（\* 	GetDocumentURL）（AIDocumentHandle文档，const char \*\* URL）
 	获得与文档关联的URL。 
- AIAPI AIErr（\* 	GetDocumentXAP）（const char \*\* xap）
 	获得当前文档的XMP元数据。 
- AIAPI AIErr（\* 	SetDocumentXAP）（const char \* xap）
 	设置当前文档的XMP元数据。 
- AIAPI AIErr（\* 	SuspendTextReflow）（）
 	暂停当前​​文档中所有文本对象的任何文本重排。 
- AIAPI AIErr（\* 	ResumeTextReflow）（）
 	恢复文本重排。 
- AIAPI AIErr（\* 	GetTextSelection）（TextRangesRef \* pTextSelection）
 	获得当前文档的文本选择对象。 
- AIAPI AIErr（\* 	HasTextFocus）（AIBoolean \* focus）
 	报告当前文档是否处于文本编辑模式。 
- AIAPI AIErr（\* 	HasTextCaret）（AIBoolean \* caret）
 	报告当前文档是处于文本编辑模式还是插入符号闪烁（与选择的文本范围相反）。 
- AIAPI AIErr（\* 	GetTextFocus）（StoryRef \* pStory）
 	获得处于编辑模式的当前故事。 
- AIAPI AIErr（\* 	SetTextFocus）（StoryRef故事）
 	将故事设置为处于编辑模式。 
- AIAPI AIErr（\* 	LoseTextFocus）（）
 	退出文本编辑模式。 
- AIAPI AIErr（\* 	GetDocumentTextResources）（DocumentTextResourcesRef \* pDocResources）
 	获得当前文档的文本资源对象。 
- AIAPI AIErr（\* 	WriteDocumentMacInformationResource）（const ai :: FilePath＆file）
- AIAPI AIErr（\* 	WriteDocumentWithOptions）（const ai :: FilePath＆file，const char \* fileFormatName，ai :: int32 options，AIBoolean askForParms）
 	将当前文档写入具有指定写入选项的文件，而不修改文档或更改其修改状态。 
- AIAPI AIErr（\* 	DocumentHasOverprint）（AIBoolean \* hasOverprint）
 	报告是否根据当前视图设置呈现当前文档需要绘制任何叠印对象。 
- AIAPI AIErr（\* 	DocumentHasManagedLinks）（AIDocumentHandle文档，AIBoolean \* hasManagedLinks）
 	报告文档是否包含任何托管链接。 
- AIAPI AIErr（\* 	GetDocumentSpotColorMode）（AIDocumentHandle文档，AISpotColorMode \*模式）
 	获得文档的专色模式，该模式控制添加到文档的新专色的外观定义。 
- AIAPI AIErr（\* 	SetDocumentSpotColorMode）（AIDocumentHandle文档，AISpotColorMode模式，AIBoolean转换，AIBoolean \*转换）
 	设置文档的专色模式，该模式控制添加到文档的新专色的外观定义。 
- AIAPI AIErr（\* 	撤消）（）
 	撤消最近的可撤消事务。 
- AIAPI AIErr（\* 	重做）（）
 	重做最近的可重做事务。 
- AIAPI AIErr（\* 	DocumentRasterAttributes）（AIBoolean \* hasDeviceNRasters，AIBoolean \* hasOverprint）
 	报告当前文档是否包含任何DeviceN或叠印栅格。 
- AIAPI AIErr（\* 	GetDocumentStartupProfile）（AIDocumentHandle文档，AIDocumentStartupProfile \* startupProfile）
 	获得文档启动配置文件。 
- AIAPI AIErr（\* 	GetDocumentBleeds）（AIRealRect \* bleedOffset）
 	获得文档的bleedOffset。 
- AIAPI AIErr（\* 	SetDocumentBleeds）（const AIRealRect＆bleedOffset）
 	设置文档的bleedOffset。 
- AIAPI AIErr（\* 	SetDocumentPixelPerfectStatus）（AIBoolean isPixelPerfect）
 	打开或关闭文档的Pixel Perfect模式。 
- AIAPI AIBoolean（\* 	GetDocumentPixelPerfectStatus）（）
 	报告文档当前是否处于Pixel Perfect模式。 
## 使用实例

```
//xxxSuites.h
#include <AIDocument.h>
extern	"C" AIDocumentSuite*			sAIDocument;

//xxxSuites.cpp
extern "C"{
AIDocumentSuite*			sAIDocument = NULL;
}

ImportSuite gImportSuites[] = {
    IMPORT_TEXT_SUITES
    kAIDocumentSuite, kAIDocumentSuiteVersion, &sAIDocument,
        nil, 0, nil
      };
//XXXPlugin.cpp
//绘制操作
sAIDocument->SyncDocument();//更新图稿属性
TextRangesRef rangesRef = NULL;
sAIDocument->GetTextSelection(&rangesRef);//获得当前文档的文本选择对象
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
