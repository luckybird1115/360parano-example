---
title: 日常知识点整理--MongoDB数据库初探
date: 2019-06-17 09:46:55
tags:
    - 数据库
categories: 日常
---

mongodb数据库初次使用

<!--more-->

## MongoDB安装

网上相关资料很多，需要自取，这里我使用ZIP安装包，解压到你想安装的目录，然后将bin目录的路径添加到环境变量中，可以在命令行中执行 mongod --help 查看是否配置成功

## 设置数据目录等

可以新建一个MongoDBData的文件夹，里面创建log和db两个子文件夹，在命令行工具中打开到bin目录，执行

```
mongod --logpath "I:\Program Files\MongoDBData\log\mongodb.log" --logappend --dbpath "I:Program Files\MongoDBData\db" --directoryperdb --install
```

## 命令行下运行MongoDB 

```
net start MongoDB
```

## 连接MongoDB

运行mongo.exe

```
I:\Program Files\mongodb-4.0.10\bin>mongo.exe
```

## 配置MongoDB服务

```cfg
systemLog:
    destination: file
    path: I:\Program Files\MongoDBData\log\mongodb.log
storage:
    dbPath: I:\Program Files\MongoDBData\db
```

```
I:\Program Files\mongodb-4.0.10\bin>mongo.exe
>--config "C:\mongodb\mongod.cfg" --install
```

但是我失败了，不管了，目测和**设置数据目录**等最找效果是一样的O(∩_∩)O

## MongoDB设置密码

1. 进入mongo.exe之后，执行show dbs可以查看现有数据库

2. `use admin`进入admin数据库

3. 创建管理员账户

   ```sql
   db.createUser({ user: "admin", pwd: "adminpassword", roles: [{ role: "userAdminAnyDatabase", db: "admin" }] })
   ```

   mongodb中的用户是基于身份role的，该管理员账户的 role是 userAdminAnyDatabase。 ‘userAdmin’代表用户管理身份，’AnyDatabase’ 代表可以管理任何数据库。

4. 验证用户是否成功添加

   `db.auth("admin", "adminpassword")` 如果返回1，则表示成功。

5. exit退出

6. 此时可以使用创建的用户和密码通过MongoDB Compass连接数据库，进行相关操作了

   



