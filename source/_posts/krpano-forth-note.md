---
title: krpano快速入门教程（四）之菜单栏
date: 2019-03-21 23:37:50
tags: krpano
categories: 全景
---
主要通过自定义plugin插件实现krpano下拉框
<!--more-->
# 背景
有时当有多个场景，用户想要直观的查看和定位指定的菜单栏，因此，最简单的方式是在页面中添加下拉框，能够支持用户选择场景进行跳转 
# 下拉式菜单栏
主要通过自定义plugin插件实现下拉框
tour.xml代码如下：
+ plugin
	plugin定义插件，其中使用了krpano中提供的combox插件，align属性可以设置下拉框相对对齐位置，onloaded定义在加载时调用的函数或动作action	
+ action fill_with_scenes
    主要读取当前所有的场景，依次设置添加到combox的item中，可以看到采用了部分krpano的语法，首先定义了item点击事件itemcall，调用loadscene加载场景，然后将场景名称和场景title作为项的属性添加
+ action select_box_item
    下拉框选中动作，如果该插件加载，则调用选中函数，否则，延时执行选中
combox.js[下载](/krpano-forth-note/combobox.js)
```xml
<krpano version="1.19" title="Virtual Tour" onstart="startup();">

<!-- combobox plugin -->
	<plugin name="box" keep="true"
	        url="%SWFPATH%/plugins/combobox.swf" width="230"
	        alturl="%SWFPATH%/plugins/combobox.js" native="false"
	        align="righttop" x="10" y="10"
	        onloaded="fill_with_scenes();"
	        />


	<!-- an action for automatically fill the combobox with all scenes -->
	<action name="fill_with_scenes">
		for(set(i,0), i LT scene.count, inc(i),
			txtadd(itemcall, 'loadscene(',get(scene[get(i)].name),',null,MERGE,BLEND(1));');
			addIdItem(get(scene[get(i)].name), get(scene[get(i)].title), get(itemcall));
		);
	</action>

	<!-- an action for updating the combobox when the scene was changed -->
	<action name="select_box_item">
		if(plugin[box].loaded,
			plugin[box].selectIdItem(%1);
		  ,
			delayedcall(0.1, select_box_item(%1));
		  );
	</action>
  
  <action name="floorvisible">
	
	
	 set(layer[map_container_mask].oy,5);
		set(layer[map_container_mask].height,100%);
  for(set(i,1);txtadd(floorname, 'floor', '_', get(i)),  layer[get(floorname)],  inc(i); txtadd(floorname, 'floor', '_', get(i)),
		  set(layer[get(floorname)].visible, false);
		  
					  		  );  
	</action>

	<!-- set skin settings: bingmaps? gyro? thumbnail controlling? tooltips? -->
	<skin_settings bingmaps="false"
	               bingmaps_key=""
	               bingmaps_zoombuttons="false"
	               gyro="true"
	               thumbs_width="120" thumbs_height="80" thumbs_padding="10" thumbs_crop="0|40|240|160"
	               thumbs_opened="false"
	               thumbs_text="false"
	               thumbs_dragging="true"
	               thumbs_onhoverscrolling="false"
	               thumbs_scrollbuttons="false"
	               thumbs_scrollindicator="false"
	               tooltips_thumbs="false"
	               tooltips_hotspots="false"
	               tooltips_mapspots="false"
	               controlbar_offset="20"
	               />

	<!-- set optional skin logo url -->
	<layer name="skin_logo" url="" scale="0.25" opened_onclick="openurl('...',_blank);" />


	<action name="startup">
		if(startscene === null, copy(startscene,scene[0].name));
		loadscene(get(startscene), null, MERGE);
	</action>

	
	<scene name="scene_1" title="场景1" onstart="" thumburl="panos/3.tiles/thumb.jpg" lat="" lng="" heading="">

		<view hlookat="0" vlookat="0" fovtype="MFOV" fov="120" maxpixelzoom="2.0" fovmin="70" fovmax="140" limitview="auto" />

		<preview url="panos/3.tiles/preview.jpg" />

		<image type="CUBE" multires="true" tilesize="512" progressive="false">
			<level tiledimagewidth="1910" tiledimageheight="1910">
				<cube url="panos/3.tiles/%s/l3/%v/l3_%s_%v_%h.jpg" />
			</level>
			<level tiledimagewidth="955" tiledimageheight="955">
				<cube url="panos/3.tiles/%s/l2/%v/l2_%s_%v_%h.jpg" />
			</level>
			<level tiledimagewidth="512" tiledimageheight="512">
				<cube url="panos/3.tiles/%s/l1/%v/l1_%s_%v_%h.jpg" />
			</level>
			<mobile>
				<cube url="panos/3.tiles/mobile_%s.jpg" />
			</mobile>
		</image>

		<!-- place your scene hotspots here -->

	</scene>
	
<scene name="scene_2" title="场景2" onstart="" thumburl="panos/1.tiles/thumb.jpg" lat="" lng="" heading="">

		<view hlookat="0" vlookat="0" fovtype="MFOV" fov="120" maxpixelzoom="2.0" fovmin="70" fovmax="140" limitview="auto" />

		<preview url="panos/1.tiles/preview.jpg" />

		<image type="CUBE" multires="true" tilesize="512" progressive="false">
			<level tiledimagewidth="955" tiledimageheight="955">
				<cube url="panos/1.tiles/%s/l2/%v/l2_%s_%v_%h.jpg" />
			</level>
			<level tiledimagewidth="512" tiledimageheight="512">
				<cube url="panos/1.tiles/%s/l1/%v/l1_%s_%v_%h.jpg" />
			</level>
			<mobile>
				<cube url="panos/1.tiles/mobile_%s.jpg" />
			</mobile>
		</image>

		<!-- place your scene hotspots here -->

	</scene>
	
</krpano>

```

### 历史文章
[krpano快速入门教程（一) 使用krpano工具构建即用型panos](/2019/03/14/krpano-first-note)  
[krpano快速入门教程（二) vtour全景漫游及vtour文件夹介绍](/2019/03/15/krpano-second-note)
[krpano快速入门教程（三）之热点Hotspot](/2019/03/16/krpano-third-note)
