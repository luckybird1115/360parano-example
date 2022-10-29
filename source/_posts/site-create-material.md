---
title: 个人博客建站
date: 2021-04-23 20:03:43
tags: site
categories: site
---

<!--more-->

# 个人建站资料

常用建站工具：http://blog.sina.com.cn/s/blog_531473f701011aim.html

## 软件准备

phpStudyhttps://www.xp.cn/download.html

【使用phpstudy时会内置一个，不需要额外下载】MySQL下载https://dev.mysql.com/downloads/mysql

**一、服务器配置**
下载PhPstudy，默认设置，自设路径安装到服务器
下载地址：https://www.xp.cn/

打开PhPstudy控制面板，开启apache和mysql服务

二、wordpress安装
wordpress下载地址：https://wordpress.org/download/

网站空间:

本机http://localhost/ 即服务器的 ……phpstudy_pro\WWW文件夹，为网站空间。非本机访问网站空间是输入ip
使用PHPstudy搭建web服务器环境时apache的默认空间路径是这个文件夹，使用不同的一键命令、编译安装、安装不同web服务器程序时的路径都不同，一般找一下home、www、html这种文件夹
windows自带的IIS也是web服务器，新接触建站这一领域的站长们基本都是从IIS认识起来，往网站空间传html文件变玩边学html,css，而后才开始慢慢接触apache, nginx, Lighttp(没试过…), 以至淘宝的tengine，而因为使用前者做web服务器的太多，使用IIS的时候出现问题不好解决。
wordpress文件迁移到网站空间

创建wordpress数据库

本机访问 http://localhost/phpmyadmin/ ，进入MySQL的Web管理端phpMyAdmin，首页新建数据库，名字任意，如 wordpress。并在“排序规则”选择 utf8_general_ci字符集。

安装wordpress

本机访问http://localhost/wordpress/ 填写刚刚创建的数据库名，用户名使用PHPstudy默认的 root，密码为root（安装完后请自行修改一下数据库密码），数据库主机即 localhost。表名前缀采用默认的 wp_ 即可，如果需要在同一个数据库中安装多个WordPress，则需要填写不同的表前缀，如 wp1_ ，wp2_

**查看与管理**
地址栏输入 http://localhost或者服务器ip/wp-admin/ 进入后台

参考资料：windows系统下wordpress建站（https://blog.csdn.net/ligongwenkenan/article/details/104707216）