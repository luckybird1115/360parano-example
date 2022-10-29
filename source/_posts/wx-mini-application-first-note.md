---
title: 微信小程序初探(一)
date: 2019-04-16 20:47:24
tags:
     - 微信小程序
categories: 微信小程序
---

<!--more-->
# 按钮绑定事件传参数
使用自定义属性，然后获取
# 列表嵌套或指定子项

# 设置属性值对于json数组中的某个属性赋值

# 微信小程序云开发第一步
  在使用开发者工具创建一个新的小程序并勾选小程序-云开发，会生成一个小程序云开发的QuickStart的小程序应用  
  按照官方提示要将cloudfunctions下的login 创建并部署到云端，然后才能获取到用户的Openid  
  刚开始尝试发现，不管是点击创建并部署云端安装依赖，还是创建并部署所有文件，点击页面中获得用户id的按钮还是会报
  errcode -404011，-504002的错误，提示你云函数没有部署成功  
  【解决方案】 在开发者工具中点击云开发，在云函数中创建同名函数，在右键login部署后，发现功能可以正常使用了
# 需要用到的帮助文档
  1. 云开发Server API文档 https://developers.weixin.qq.com/miniprogram/dev/wxcloud/reference-server-api/
  2. 云开发指引文档 https://developers.weixin.qq.com/miniprogram/dev/wxcloud/basis/getting-started.html
  3. 服务端接口文档 https://developers.weixin.qq.com/miniprogram/dev/api-backend/
  4. 小程序API文档  https://developers.weixin.qq.com/miniprogram/dev/api/
  5. 小程序组件文档 https://developers.weixin.qq.com/miniprogram/dev/component/
  6. 小程序基础指南 https://developers.weixin.qq.com/miniprogram/dev/
