---
title: AI组件开发（二）--AIRealMathSuite
date: 2019-03-18 10:50:27
tags: 
  - AI  
categories: AI组件开发
---
主要介绍AIRealMathSuite中使用到的函数，以及部分应用场景，持续更新中
<!--more-->
# AIRealRectOverlap
AIAPI AIBoolean(\* AIRealMathSuite::AIRealRectOverlap)(const AIRealRect \*a, const AIRealRect \*b)
Tests whether two rectangles overlap (have any points in common).
测试两个矩形是否重叠（有任何共同点）  
+ 参数:
  + a   	
The first rectangle.
  + b   	
The second rectangle.  
+ 返回值：  
如果矩形重叠，则为真。

# AIRealRectInAIRealRect
AIAPI AIBoolean(\* AIRealMathSuite::AIRealRectInAIRealRect)(const AIRealRect \*a, const AIRealRect \*b)
Tests whether one rectangle is inside (entirely contained in) another rectangle.  
测试一个矩形是否在另一个矩形内（完全包含在内）。  
 
Both must be open or both closed.  
两者都必须是开放的或都是封闭的。
+ 参数:
  + a   	
   The first rectangle.  
  + b   
   The second rectangle.
+ 返回值：
True if the set of points contained by a is also contained by b.
如果a包含的点集也包含在b内，则为真。

# AIRealRectSet
AIAPI void(\* AIRealMathSuite::AIRealRectSet)(AIRealRect \*a, AIReal left, AIReal top, AIReal right, AIReal bottom)
Sets the coordinate values in a rectangle. 设置矩形中的坐标值
(In the Illustrator art coordinate system, the origin, (0, 0), is at the bottom left corner of a page. X and Y values increase upward and to the right.) 
+ 参数:
    + a   
    The rectangle object. ///AI的矩形对象指针
    + left   
    The left side location.///xmin
    + top   
    The top side location. ///ymax
    + right   
    The right side location.///xmax
    + bottom   
    The bottom side location.///ymin
    
注意：Illustrator的坐标系原点位于页面左下角，X和Y值向上和向右增加。
# AIRealPointInterpolate
AIAPI void（\* AIRealMathSuite :: AIRealPointInterpolate）（const AIRealPoint \* a，const AIRealPoint \* b，AIReal t，AIRealPoint \* result）
通过在点的相应坐标之间插入新坐标值来计算两点之间的点的位置。
原理就是向量
结果是 a * t + b * (1-t)

参数：
a 第一点。
b 第二点。
t（b-a）的百分比，表示为0到1之间的数字。
result 	[out\]用于返回结果的缓冲区。
# 参考资料
  Adobe Illustrator SDK，下载地址：https://www.adobe.com/devnet/illustrator/sdk.html
# 相关文章
[AI组件开发（一）--注记旋转矩阵](/2019/03/14/ai-first-note)  
[AI组件开发（二）--AIRealMathSuite](/2019/03/18/ai-second-note)
[AI组件开发（三）--AIArtboardSuite](/2019/03/24/ai-third-note)
[AI组件开发（四）--AIDocumentSuite](/2019/03/24/ai-forth-document-note)  
[AI组件开发（五）--AILayerSuite](/2019/03/24/ai-fifth-ailayer-note)
[AI组件开发（六）--AIArtSuite](/2019/03/24/ai-six-aiart-note)
[AI组件开发（七）--AIPathSuite&&AIPathfinderSuite](/2019/03/25/ai-seven-path-note)  
[AI组件开发（八）--AIArtStyleSuite](/2019/03/25/ai-eight-style-note)
