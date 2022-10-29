---
title: Vue全局添加组件
date: 2019-03-09 10:39:22
tags: Vue
categories: 前端
---
Vue全局添加组件，方便重复使用的功能一次性定义
<!--more-->
### 一. 背景  
在项目中有时需要反复用到如区域选择器等组件，每次使用时都需要在对应的vue中添加import,并在component中注册，为了避免多次书写引用代码，可以自定义全局组件，只需要在main.js中一次性注册在vue项目中，可以自定义组件像vue-resource一样使用Vue.use（）方法来使用。
### 二. 具体实现方法  
1、首先建一个自定义组件的文件夹，比如叫loading，在该文件夹下新建一个index.js，一个自定义组件loading.vue,在这个loading.vue里面就是这个组件的具体的内容，

2、在index.js中，规定使用这个组件的名字，以及使用方法，如：
 
    import loadingComponent from './loading.vue'
     
    const loading={
        install:function(Vue){
            Vue.component('Loading',loadingComponent)
        }  //'Loading'这就是后面可以使用的组件的名字，install是默认的一个方法
    };
    export default loading;
3、只要在main.js中规定了install方法，就可以像一些公共的插件一样使用Vue.use()来使用：
 
    import loading from './loading'
    Vue.use(loading）

