---
title: krpano快速入门教程（三）之热点Hotspot
date: 2019-03-16 22:54:57
tags: krpano
categories: 全景
---
使用编辑器热点，添加自定义热点，动态热点等
<!--more-->
## 添加热点及相关内容  
使用编辑器热点，添加自定义热点，动态热点等
<!--more-->
### 使用tour_editor添加热点及设定起始角度
通过__Load tour.xml__导入要编辑的配置文件  
点击__set as startup view__设置初始角度，可以旋转到合适的角度，点击按钮设置全景图默认展示角度。  
点击__Add hotspot__按钮，画面中出现一个箭头，将其拖放置你想放置的地方，点击__save__按钮保存热点编辑。也可以通过编辑按钮设置热点链接场景。  
点击__Save tour.xml__按钮保存文件。
### 编辑 tour.xml添加热点
在实际生产中，通常直接编辑xml文件，在相应的场景中添加热点
```
<hotspot name="..."              定义热点名称    
         type="image"            定义热点类型，只能是image    
         url="..."               热点图像路径，支持SWF, JPG, PNG, GIF    
         alturl="..."            html5状态下显示的图像路径    
         keep="false"            是否在下一场景跳转后保持显示    
         devices="all"           支持设备类型    
         visible="true"          是否可见    
         enabled="true"          设置热点是否接收鼠标事件    
         handcursor="true"       设置是否鼠标移到上面显示小手    
         maskchildren="false"    设置是否将子控件变成蒙板    
         zorder=""               插入元素的次序索引，可以是字符也可以是数字，html5输出必须是0-100整数    
         zorder2="0.0"           设置为0.0和1.0，分别对应当前热点在扭曲控件之下和之上    
         capture="true"          与enabled配合使用，都为true只对热点传递动作，capture="false"可对子控件传递动作 
         children="true"         设置子控件是否接收热点鼠标事件    
         blendmode="normal"      设置混合模式，可选：normal, layer, screen, add, subtract, difference, multiply, overlay, lighten, darken, hardlight, invert.    
    
         style=""                读入已设置好的style名称    
         ath="0.0" atv="0.0"     设定将场景缩略图为球形热点    
         edge="center"           热点的边界对齐点    
         ox="0"                  边界到对齐点的偏移量    
         oy="0"                      
         zoom="false"            设置是否场景缩放时，热点跟随缩放    
         distorted="false" rx="0.0" ry="0.0" rz="0.0"    设置热点是否跟随场景进行3D扭曲    
         details="8"              热点显示的细节数量，默认为8    
         inverserotation="false"  设置是否逆向旋转3D扭曲    
         flying="0.0"             设置ath/atv/scale的插值     
         width="" height=""       设置热点宽高    
         scale="1.0"              设置热点缩放    
         rotate="0.0"             设置热点旋转角度    
         pixelhittest="false"     是否启动精确像素测试    
         smoothing="true"         设置缩放时，是否平滑化处理    
         accuracy="0"             设置像素渲染值（flash下）    
         accuracy2="1"            设置精度控制（html5下）    
         alpha="1.0"              设置透明度    
         autoalpha="false"        设置是否自动变更透明度    
         usecontentsize="false"   是否使用用户指定flash大小，默认false原始大小    
         scale9grid=""            定义是否支持矢量缩放，scale9grid="x-position|y-position|width|height|prescale*"    
         crop=""                  定义元素坐标及宽高  crop="x-position|y-position|width|height"    
         onovercrop=""          设置元素鼠标移到上方后的坐标及宽高    
         ondowncrop=""          设置元素鼠标按下状态后的坐标及宽高    
         scalechildren="false"   子元素是否跟随缩放    
         mask=""                 mask="name"  mask="layer[name]"  mask="hotspot[name]"    
         effect=""                热点位图效果3种：glow(color,alpha,range,strength);    
                                  dropshadow(depth,angle,color,range,strength);    
                                  blur(radius);    	    
         onover=""               鼠标在经过上方时执行动作    
         onhover=""              鼠标停在上方时执行动作    
         onout=""                鼠标停移出范围时时执行动作    
         onclick=""              鼠标停点击时执行动作    
         ondown=""               鼠标按下时执行动作    
         onup=""                 鼠标按键松开时执行动作    
         onloaded=""             加载元素完成后执行动作    
	         />    
	    	 	  	    
	多边形热点    
	    
	    
	<hotspot name="..."    
	         keep="false"    
	         visible="true"    
	         enabled="true"    
	         handcursor="true"    
	         zorder=""    
	         capture="true"    
	         blendmode="normal"    
	         style=""    
	         alpha="1.0"    
	         autoalpha="false"    
	         fillcolor="0xFFFFFF" fillcolorhover="0xFFFFFF"   填充颜色及鼠标移上后颜色    
	         fillalpha="0.0" fillalphahover="0.1"             填充颜色透明度及鼠标移上后颜色透明度    
	         borderwidth="0.0" borderwidthhover="4.0"         边界宽度及鼠标移上后边界宽度    
	         bordercolor="0xFFFFFF" bordercolorhover="0xFFFFFF"  边界颜色及边界鼠标移上后颜色    
	         borderalpha="0.0" borderalphahover="0.8"           边界透明度及鼠标移上后边界透明度    
	         fadeintime="0.15" fadeincurve="1.1"               悬停状态变化时间及形式    
	         fadeouttime="0.3" fadeoutcurve="0.7"    
	         onover=""    
	         onhover=""    
	         onout=""    
	         onclick=""    
	         ondown=""    
	         onup=""    
	>    
	    <point ath="..." atv="..." />            多边形热点坐标    
	    <point ath="..." atv="..." />    
	    <point ath="..." atv="..." />    
	    ...    
	</hotspot>  
```
### 历史文章
[krpano快速入门教程（一) 使用krpano工具构建即用型panos](/2019/03/14/krpano-first-note)  
[krpano快速入门教程（二) vtour全景漫游及vtour文件夹介绍](/2019/03/15/krpano-second-note)
