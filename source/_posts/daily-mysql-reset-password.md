---
title: 日常整理之mysql密码重置
date: 2019-06-16 21:29:36
tags: 
    - mysql
categories: 日常
---

本人没有保管号自己mysql的密码，致使无法登陆上

<!--more-->

### mysql密码重置

#### 1. 修改my.ini 文件

在安装目录（如 I:\Program Files\mysql-8.0.15-winx64）下找到或新建my.ini；在[mysqld]段下面加入skip-grant-tables，并关闭mysql服务

```ini
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
 
[mysqld]
skip-grant-tables
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=I:\\Program Files\\mysql-8.0.15-winx64
# 设置 mysql数据库的数据的存放目录，MySQL 8+ 不需要以下配置，系统自己生成即可，否则有可能报错
datadir=I:\\Program Files\\mysql-8.0.15-winx64\data
# datadir=C:\\web\\sqldata
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

#### 2. 使用cd指令切换到mysql的bin目录

#### 3. 执行mysqld  --console --skip-grant-tables --shared-memory

![1560692598969](daily-mysql-reset-password\1560692598969.png)

#### 4. 在以管理员身份打开一个cmd命令行工具，输入mysql

此时不需要密码几个直接进入

#### 5.  使用 use mysql,然后执行一下命令可以查看密码

```
select user,host,authentication_string from user
```

#### 6. 修改密码 `ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码';`

![1560693166712](daily-mysql-reset-password\1560693166712.png)

#### 7. 执行`flush privileges `

#### 8. quit 退出

#### 9. 回到第一个命名行窗口 ctrl+c，停止，可以将my.ini中skip-grant-tables删除，之后再使用net start mysql启动

#### 10. mysql -u root -p 输入新密码登录
