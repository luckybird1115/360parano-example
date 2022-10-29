---
title: sqlite数据库查询 C++函数
date: 2019-03-17 21:17:22
tags:
    - sqlite
    - C++
categories: 其他
---
## sqlite部分函数简介
主要有打开数据库连接，执行数据库查询语句，关闭连接等函数
<!--more-->
### sqlite3_open用法
打开数据库链接  
sqlite3_open用法  
原型：  
```
int sqlite3_open(
  const char *filename,   /* Database filename (UTF-8) */
  sqlite3 **ppDb          /* OUT: SQLite db handle */
);
```
用这个函数开始数据库操作。需要传入两个参数，一是数据库文件名，比如：E:/test.db。文件名不需要一定存在，如果此文件不存在，sqlite会自动建立它。如果它存在，就尝试把它当数据库文件来打开。  
二是sqlite3**，即前面提到的关键数据结构。  
函数返回值表示操作是否正确，如果是SQLITE_OK则表示操作正常。相关的返回值sqlite定义了一些宏。具体这些宏的含义可以参考sqlite3.h 文件。里面有详细定义。
### sqlite3_close用法
关闭数据库链接  
原型：  
```
int sqlite3_close(sqlite3 *ppDb);
```
ppDb为刚才使用sqlite3_open打开的数据库链接  
### sqlite3_exec用法
执行sql操作  
原型：  
```
int sqlite3_exec(
  sqlite3* ppDb,                             /* An open database */
  const char *sql,                           /* SQL to be evaluated */
  int (*callback)(void*,int,char**,char**),  /* Callback function */
  void *,                                    /* 1st argument to callback */
  char **errmsg                              /* Error msg written here */
);
```
这就是执行一条sql 语句的函数。  
第1个参数不再说了，是前面open函数得到的指针。  
第2个参数constchar\*sql是一条sql 语句，以\0结尾。    
第3个参数sqlite3_callback 是回调，当这条语句执行之后，sqlite3会去调用你提供的这个函数。  
第4个参数void\*是你所提供的指针，你可以传递任何一个指针参数到这里，这个参数最终会传到回调函数里面，如果不需要传递指针给回调函数，可以填NULL。回调函数的写法，以及这个参数的使用在之后介绍。  
第5个参数char\*\* errmsg 是错误信息。注意是指针的指针。sqlite3里面有很多固定的错误信息。执行sqlite3_exec 之后，执行失败时可以查阅这个指针（直接cout<<errmsg得到一串字符串信息，这串信息告诉你错在什么地方。sqlite3_exec函数通过修改你传入的指针的指针，把你提供的指针指向错误提示信息，这样sqlite3_exec函数外面就可以通过这个char\*得到具体错误提示。  
说明：通常，sqlite3_callback 和它后面的void\*这两个位置都可以填NULL。填NULL表示你不需要回调。比如你做insert 操作，做delete操作，就没有必要使用回调。而当你做select 时，就要使用回调，因为sqlite3 把数据查出来，得通过回调告诉你查出了什么数据。
exec 的回调  
`typedef int(*sqlite3_callback)(void*,int,char**,char**);`  
你的回调函数必须定义成上面这个函数的类型。下面给个简单的例子：
```
//sqlite3的回调函数
//sqlite 每查到一条记录，就调用一次这个回调
int LoadMyInfo(void* para,intn_column,char** column_value,char** column_name);
//para是你在sqlite3_exec 里传入的void*参数通过para参数，你可以传入一些特殊的指针（比如类指针、结构指针），
//然后在这里面强制转换成对应的类型（这里面是void*类型，必须强制转换成你的类型才可用）。然后操作这些数据
//n_column是这一条记录有多少个字段(即这条记录有多少列)
//char** column_value 是个关键值，查出来的数据都保存在这里，它实际上是个1维数组（不要以为是2维数组），
//每一个元素都是一个char*值，是一个字段内容（用字符串来表示，以\0结尾）
//char** column_name 跟column_value是对应的，表示这个字段的字段名称
```
实例：
```
#include <iostream>
using namespace std;
#include "sqlite/sqlite3.h"
int callback(void*,int,char**,char**);
int main()
{
    sqlite3* db;
    int nResult = sqlite3_open("test.db",&db);
    if (nResult != SQLITE_OK)
    {
        cout<<"打开数据库失败："<<sqlite3_errmsg(db)<<endl;
        return 0;
    }
    else
    {
        cout<<"数据库打开成功"<<endl;
    }

    char* errmsg;

    nResult = sqlite3_exec(db,"create table fuck(id integer primary key autoincrement,name varchar(100))",NULL,NULL,&errmsg);
     if (nResult != SQLITE_OK)
     {
         sqlite3_close(db);
         cout<<errmsg;
         sqlite3_free(errmsg);
        return 0;
    }
    string strSql;
    strSql+="begin;\n";
    for (int i=0;i<100;i++)
    {
        strSql+="insert into fuck values(null,'heh');\n";
    }
    strSql+="commit;";
    //cout<<strSql<<endl;

    nResult = sqlite3_exec(db,strSql.c_str(),NULL,NULL,&errmsg);

    if (nResult != SQLITE_OK)
    {
        sqlite3_close(db);
        cout<<errmsg<<endl;
        sqlite3_free(errmsg);
        return 0;
    }

    strSql = "select * from fuck";
    nResult = sqlite3_exec(db,strSql.c_str(),callback,NULL,&errmsg);
      if (nResult != SQLITE_OK)
    {
        sqlite3_close(db);
        cout<<errmsg<<endl;
        sqlite3_free(errmsg);
        return 0;
    }

    sqlite3_close(db);
    return 0;
}

int callback(void* ,int nCount,char** pValue,char** pName)
{
    string s;
    for(int i=0;i<nCount;i++)
    {
        s+=pName[i];
        s+=":";
        s+=pValue[i];
        s+="\n";
    }
    cout<<s<<endl;
    return 0;
}
```



