---
title: AI组件开发（九）--AIDocumentListSuite
date: 2019-04-19 20:41:28
tags: AI
categories: AI组件开发
---

<!--more-->
# DocumentListSuite创建文档
  使用场景，通常我们写的插件在创建图层或者绘制时，需要判断当前时候存在文档且文档是否处于激活状态，没有这需要创建然后进行之后的操作  
  
  AIDocumentListSuite中的New方法能够很好的满足我们的需要。
  ```
  //判断当前是否有文档
  	AIDocumentHandle currentDocument;
  	ai::int32 count=0;
  	sAIDocumentList->Count(&count);
  	if (count == 0){
  		sAIDocumentList->New(ai::UnicodeString(""), NULL, kDialogNone, &currentDocument);
  	}
  ```
## 使用默认设置创建
```
sAIDocumentList->New(ai::UnicodeString(""), NULL, kDialogOff, &currentDocument);
```
## 自定义文档属性创建
这里比较坑，根据官网api文档，如果文档参数不为空时，必须完整的定义NewDocumentPreset
实现如下所示
```
AINewDocumentPreset newDocumentPreset;
	newDocumentPreset.docTitle = ai::UnicodeString(L"新建文档");
	newDocumentPreset.docWidth = 500;
	newDocumentPreset.docHeight = 250;
	newDocumentPreset.docNumArtboards = 1;
	newDocumentPreset.docArtboardLayout = kAIArtboardLayoutCol;
	newDocumentPreset.docArtboardSpacing = 1;
	newDocumentPreset.docArtboardRowsOrCols = 1;
	newDocumentPreset.docColorMode = kAICMYKColorModel;
	newDocumentPreset.docUnits = kMillimetersUnits;
	newDocumentPreset.docPreviewMode = kAIPreviewModeDefault;
	newDocumentPreset.docTransparencyGrid = kAITransparencyGridNone;
	newDocumentPreset.docRasterResolution = kAIRasterResolutionHigh;
	sAIDocumentList->New(ai::UnicodeString(L"新建文档") , &newDocumentPreset, kDialogOff, &currentDocument);
```

