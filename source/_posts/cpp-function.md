
title: C++部分基础知识
date: 2019-03-17 14:09:53
tags: C++
categories: C++
---
介绍项目中用到的一些常用函数和关键字的使用
<!--more-->
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [常用函数](#%E5%B8%B8%E7%94%A8%E5%87%BD%E6%95%B0)
  - [1. strtok](#1-strtok)
  - [2.strcmp](#2strcmp)
  - [3. Erase](#3-erase)
  - [4. List](#4-list)
  - [5. strcpy](#5-strcpy)
  - [6.转换函数](#6%E8%BD%AC%E6%8D%A2%E5%87%BD%E6%95%B0)
    - [6.1 Atoi](#61-atoi)
- [关键字](#%E5%85%B3%E9%94%AE%E5%AD%97)
  - [extern](#extern)
    - [变量](#%E5%8F%98%E9%87%8F)
    - [函数](#%E5%87%BD%E6%95%B0)
    - [编译链接](#%E7%BC%96%E8%AF%91%E9%93%BE%E6%8E%A5)
      - [声明外部变量](#%E5%A3%B0%E6%98%8E%E5%A4%96%E9%83%A8%E5%8F%98%E9%87%8F)
    - [C++中extern c的深层探索](#c%E4%B8%ADextern-c%E7%9A%84%E6%B7%B1%E5%B1%82%E6%8E%A2%E7%B4%A2)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

---
# 常用函数
## 1. strtok  
  分解字符串为一组字符串。s为要分解的字符，delim为分隔符字符（如果传入字符串，则传入的字符串中每个字符均为分割符）。首次调用时，s指向要分解的字符串，之后再次调用要把s设成NULL。  
  例如：strtok("abc,def,ghi",",")，最后可以分割成为abc def ghi.尤其在点分十进制的IP中提取应用较多。
strtok的函数原型为char *strtok(char *s, char *delim)，功能为“Parse S into tokens separated by characters in DELIM.If S is NULL, the saved pointer in SAVE_PTR is used as the next starting point. ” 翻译成汉语就是：作用于字符串s，以包含在delim中的字符为分界符，将s切分成一个个子串；如果，s为空值NULL，则函数保存的指针SAVE_PTR在下一次调用中将作为起始位置。
```
#include<iostream>
#include<cstring>
using namespace std;
int main()
{
    char sentence[]="This is a sentence with 7 tokens";
    cout << "The string to be tokenized is:\n" << sentence << "\n\nThe tokens are:\n\n";
    char *tokenPtr=strtok(sentence," ");
    while(tokenPtr!=NULL)　{
        cout<<tokenPtr<<endl;
        tokenPtr=strtok(NULL," ");
    }
    //cout << "After strtok,sentence=" << tokenPtr<<endl;
    return 0;
}
```
函数第一次调用需设置两个参数。第一次分割的结果，返回串中第一个 ',' 之前的字符串,也就是上面的程序第一次输出abc。
第二次调用该函数strtok(NULL,","),第一个参数设置为NULL。结果返回分割依据后面的字串，即第二次输出d。
strtok是一个线程不安全的函数，因为它使用了静态分配的空间来存储被分割的字符串位置
线程安全的函数叫strtok_r,ca
运用strtok来判断ip或者mac的时候务必要先用其他的方法判断'.'或':'的个数，因为用strtok截断的话，比
## 2.strcmp
C/C++函数，比较两个字符串
设这两个字符串为str1，str2，
若str1=str2，则返回零；
若str1<str2，则返回负数；
若str1>str2，则返回正数。
matlab中函数，strcmp(s1，s2) 判断两个字符串s1和s2是否相同，相同返回true ,不同返回false  
源码：
```c
int strcmp(const char *str1,const char *str2)
{
    /*不可用while(*str1++==*str2++)来比较，当不相等时仍会执行一次++，
    return返回的比较值实际上是下一个字符。应将++放到循环体中进行。*/
    while(*str1 == *str2)
    {
                assert((str1 != NULL) && (str2 != NULL));
                 
        if(*str1 == '\0')
            return 0;
         
        str1++;
        str2++;
    }
    return *str1 - *str2;
}
```
## 3. Erase
* c.erase(k)  
从c中删除元素k，返回一个size_type值，指出删除的元素的数量
* c.erase(p)  
从c中删除迭代器p指定的元素，p必须指向c中的一个真实元素，不能等于c.end()
* c.erase(b,e)  
从c中删除迭代器对b和e所表示的范围中的元素，返回e
## 4. List
List 反向迭代器  
begin和end成员  
begin和end操作产生指向容器内第一个元素和最后一个元素的下一个位置的迭代器，如下所示。这两个迭代器通常用于标记包含容器中所有元素的迭代范围。  
c.begin() 返回一个迭代器，它指向容器c的第一个元素  
c.end() 返回一个迭代器，它指向容器c的最后一个元素的下一个位置  
c.rbegin() 返回一个逆序迭代器，它指向容器c的最后一个元素  
c.rend() 返回一个逆序迭代器，它指向容器c的第一个元素前面的位置  
上述每个操作都有两个不同的版本：一个是const成员，另一个是非const成员。这些操作返回什么类型取决于容器是否为const。如果容器不是const，则这些操作返回iterator或reverse_iterator类型。如果容器是const，则其返回类型要加上const_前缀，也就是const_iterator和const_reverse_iterator类型。  
## 5. strcpy
描述  
C 库函数 char *strcpy(char *dest, const char *src) 把 src 所指向的字符串复制到 dest。

声明  
下面是 strcpy() 函数的声明。  
`char *strcpy(char *dest, const char *src)`  
参数  
dest -- 指向用于存储复制内容的目标数组。  
src -- 要复制的字符串。  
返回值  
该函数返回一个指向最终的目标字符串 dest 的指针。  
实例  
下面的实例演示了 strcpy() 函数的用法。  
```
#include <stdio.h>
#include <string.h>

int main()
{
   char src[40];
   char dest[100];
  
   memset(dest, '\0', sizeof(dest));
   strcpy(src, "This is runoob.com");
   strcpy(dest, src);

   printf("最终的目标字符串： %s\n", dest);
   
   return(0);
}
```
## 6.转换函数
### 6.1 Atoi
字符串转整型
### 6.2 Atof
字符串转浮点型
## 7. malloc动态内存分配
用于申请一块连续的指定大小的内存块区域以void*类型返回分配的内存区域地址，当无法知道内存具体位置的时候，想要绑定真正的内存空间，就需要用到动态的分配内存。  
void \*malloc(size_t size);  
如果分配成功则返回指向被分配内存的指针(此存储区中的初始值不确定)，否则返回空指针NULL。当内存不再使用时，应使用free()函数将内存块释放。函数返回的指针一定要适当对齐，使其可以用于任何数据对象。
例：  
```
int *p;
p = (int*)malloc(sizeof(int) * 128);
//分配128个（可根据实际需要替换该数值）整型存储单元，
//并将这128个连续的整型存储单元的首地址存储到指针变量p中
double *pd = (double*)malloc(sizeof(double) * 12);
//分配12个double型存储单元，
//并将首地址存储到指针变量pd中
```
## math库中的函数
### hypot
 计算直角三角形的斜边长
# 关键字
## extern
extern是计算机语言中的一个关键字，可置于变量或者函数前，以表示变量或者函数的定义在别的文件中。提示编译器遇到此变量或函数时，在其它模块中寻找其定义，另外，extern也可用来进行链接指定。  
### 变量
在一个源文件里定义了一个数组：char a\[6\];  
在另外一个文件里用下列语句进行了声明：extern char \*a；  
    请问，这样可以吗？  
    答案与分析：  
    1)、不可以，程序运行时会告诉你非法访问。原因在于，指向类型T的指针并不等价于类型T的数组。extern char \*a声明的是一个指针变量而不是字符数组，因此与实际的定义不同，从而造成运行时非法访问。应该将声明改为extern char a[ ]。  
    2)、例子分析如下，如果a[] = "abcd",则外部变量a=0x12345678 (数组的起始地址)，而\*a是重新定义了一个指针变量，a指向的地址可能是0x87654321,直接使用\*a是错误的.  
    3)、这提示我们，在使用extern时候要严格对应声明时的格式，在实际编程中，这样的错误屡见不鲜。  
    4)、extern用在变量声明中常常有这样一个作用：你要在\*.c文件中引用另一个文件中的一个全局的变量，那就应该放在\*.h中用extern来声明这个全局变量。  
    extern用于变量的用法：  
    extern int a;//声明一个全局变量a  
    int a; //定义一个全局变量a  
    extern int a =0 ;//定义一个全局变量a 并给初值。一旦给予赋值，一定是定义，定义才会分配存储空间。（注意：经过测试在GCC中，这样定义变量是不能通过编译的，而在VS2013可以）  
    int a =0;//定义一个全局变量a,并给初值，  
    声明之后你不能直接使用这个变量，需要定义之后才能使用。  
    第四个等于第三个，都是定义一个可以被外部使用的全局变量，并给初值。  
    糊涂了吧，他们看上去可真像。但是定义只能出现在一处。也就是说，不管是int a;还是int a=0;都只能出现一次，而那个extern int a可以出现很多次。  
    当你要引用一个全局变量的时候，你就要声明extern int a;这时候extern不能省略，因为省略了，就变成int a;这是一个定义，不是声明。  
### 函数
实际上函数的声明和定义都不需要添加extern关键字，在实际使用的时候也最好不要添加关键字。  
如果一个函数是不会被其它文件调用的，那么这个函数应该被声明成static的。  
如：`extern int func(void){return 0;}`跟`int func(void){return 0;}`    是等价的  
另外  `extern int func(void);` 跟`int func(void);`是等价的。  
### 编译链接
#### 声明外部变量
现代编译器一般采用按文件编译的方式，因此在编译时，各个文件中定义的全局变量是互相不透明的。也就是说，在编译时，全局变量的可见域限制在文件内部。  

下面举一个简单的例子：

创建一个工程，里面含有A.cpp和B.cpp两个简单的C++源文件：
```
//A.cpp
int i;
int main(){
}
//B.cpp
int i;
```
这两个文件极为简单，在A.cpp中我们定义了一个全局变量i，在B中我们也定义了一个全局变量i。 
 
我们对A和B分别编译，都可以正常通过编译，但是进行链接的时候，却出现了错误，错误提示如下：  
```
Linking...
B.obj:error LNK2005:"inti"(?i@@3HA)already defined in A.obj
Debug/A.exe:fatal error LNK1169:one or more multiply defined symbols found
Error executing link.exe.
A.exe-2 error(s),0 warning(s)
```
这就是说，在编译阶段，各个文件中定义的全局变量相互是不透明的，编译A时觉察不到B中也定义了i，同样，编译B时觉察不到A中也定义了i。  

但是到了链接阶段，要将各个文件的内容“合为一体”，因此，如果某些文件中定义的全局变量名相同的话，在这个时候就会出现错误，也就是上面提示的重复定义的错误。 
 
因此，各个文件中定义的全局变量名不可相同。 
 
在链接阶段，各个文件的内容（实际是编译产生的obj文件）是被合并到一起的，因而，定义于某文件内的全局变量，在链接完成后，它的可见范围被扩大到了整个程序。  

这样一来，按道理说，一个文件中定义的全局变量，可以在整个程序的任何地方被使用，举例说，如果A文件中定义了某全局变量，那么B文件中应可以使用该变量。修改我们的程序，加以验证：  

```
//A.cpp
int main()
{
    i = 100;//试图使用B中定义的全局变量
}
//B.cpp
int i;
```
编译结果如下:
```
Compiling...
A.cpp
C:\Documents and Settings\桌面\try extern\A.cpp(5):error C2065:'i':undeclared identifier
Error executing cl.exe.
A.obj-1 error(s),0 warning(s)
```
编译错误。 
 
其实出现这个错误是意料之中的，因为文件中定义的全局变量的可见性扩展到整个程序是在链接完成之后，而在编译阶段，他们的可见性仍局限于各自的文件。  

编译器的目光不够长远，编译器没有能够意识到，某个变量符号虽然不是本文件定义的，但是它可能是在其它的文件中定义的。  

虽然编译器不够有远见，但是我们可以给它提示，帮助它来解决上面出现的问题。这就是extern的作用了。
  
extern的原理很简单，就是告诉编译器：“你现在编译的文件中，有一个标识符虽然没有在本文件中定义，但是它是在别的文件中定义的全局变量，你要放行！” 
 
我们为上面的错误程序加上extern关键字：  
    ```
    //A.cpp
    extern int i;
    int main()
    {
        i=100;//试图使用B中定义的全局变量
    }
    //B.cpp
    int i;
    ```
顺利通过编译，链接。
### C++中extern c的深层探索
1. 简介
 C++语言的创建初衷是“a better C”，但是这并不意味着C++中类似C语言的全局变量和函数所采用的编译和连接方式与C语言完全相同。作为一种欲与C兼容的语言，C++保留了一部分过程式语言的特点（被世人称为“不彻底地面向对象”），因而它可以定义不属于任何类的全局变量和函数。但是，C++毕竟是一种面向对象的程序设计语言，为了支持函数的重载，C++对全局函数的处理方式与C有明显的不同。 
2. 从标准头文件说起  
某企业曾经给出如下的一道面试题：  
为什么标准头文件都有类似以下的结构？
    ```
    #ifndef __INCvxWorksh
    #define __INCvxWorksh
    #ifdef __cplusplus
    extern "C" {
    #endif
    /*...*/
    #ifdef __cplusplus
    }
    #endif
    #endif /* __INCvxWorksh */
    ```
    分析  
    显然，头文件中的编译宏“#ifndef __INCvxWorksh、#define __INCvxWorksh、#endif” 的作用是防止该头文件被重复引用。  
    那么    
    ```
    #ifdef __cplusplus
    extern "C" {
    #endif
    #ifdef __cplusplus
    }
    #endif
    ```
    的作用又是什么呢？我们将在下文一一道
3. 深层揭密extern "C"  
    extern "C" 包含双重含义，从字面上即可得到：首先，被它修饰的目标是“extern”的；其次，被它修饰的目标是“C”的。让我们来详细解读这两重含义。  
    
    被extern "C"限定的函数或变量是extern类型的；
    
    extern是C/C++语言中表明函数和全局变量作用范围（可见性）的关键字，该关键字告诉编译器，其声明的函数和变量可以在本模块或其它模块中使用，记住，下列语句：
    extern int a;
    仅仅是一个变量的声明，其并不是在定义变量a，并未为a分配内存空间。变量a在所有模块中作为一种全局变量只能被定义一次，否则会出现连接错误。
    
    引用一个定义在其它模块的全局变量或函数（如，全局函数或变量定义在A模块，B欲引用）有两种方法，  
    一、B模块中include模块A的头文件。  
    二、模块B中对欲引用的模块A的变量或函数重新声明一遍，并前加extern关键字。  
    
    通常，在模块的头文件中对本模块提供给其它模块引用的函数和全局变量以关键字extern声明。  
    例如，如果模块B欲引用该模块A中定义的全局变量和函数时只需包含模块A的头文件即可。这样，模块B中调用模块A中的函数时，在编译阶段，模块B虽然找不到该函数，但是并不会报错；它会在连接阶段中从模块A编译生成的目标代码中找到此函数。
    
    与extern对应的关键字是static，被它修饰的全局变量和函数只能在本模块中使用。因此，一个函数或变量只可能被本模块使用时，其不可能被extern “C”修饰。
    
    被extern "C"修饰的变量和函数是按照C语言方式编译和连接的；  
    未加extern “C”声明时的编译方式 
     
    首先看看C++中对类似C的函数是怎样编译的。  
    
    作为一种面向对象的语言，C++支持函数重载，而过程式语言C则不支持。函数被C++编译后在符号库中的名字与C语言的不同。例如，假设某个函数的原型为：
    `void foo( int x, int y );`  
    该函数被C编译器编译后在符号库中的名字为_foo，而C++编译器则会产生像_foo_int_int之类的名字（不同的编译器可能生成的名字不同，但是都采用了相同的机制，生成的新名字称为“mangled name”）。  
    _foo_int_int这样的名字包含了函数名、函数参数数量及类型信息，C++就是靠这种机制来实现函数重载的。例如，在C++中，函数`void foo( int x, int y )`与`void foo( int x, float y )`编译生成的符号是不相同的，后者为_foo_int_float。  
    
    同样地，C++中的变量除支持局部变量外，还支持类成员变量和全局变量。用户所编写程序的类成员变量可能与全局变量同名，我们以"."来区分。而本质上，编译器在进行编译时，与函数的处理相似，也为类中的变量取了一个独一无二的名字，这个名字与用户程序中同名的全局变量名字不同。
    
    未加extern "C"声明时的连接方式
    
    假设在C++中，模块A的头文件如下：
    ```
    // 模块A头文件　moduleA.h
    #ifndef MODULE_A_H
    #define MODULE_A_H
    int foo( int x, int y );
    #endif
    在模块B中引用该函数：
    // 模块B实现文件　moduleB.cpp
    #include "moduleA.h"
    foo(2,3);
    ```
    实际上，在连接阶段，连接器会从模块A生成的目标文件moduleA.obj中寻找_foo_int_int这样的符号！
    
    加extern "C"声明后的编译和连接方式
    加extern "C"声明后，模块A的头文件变为：
    ```
    // 模块A头文件　moduleA.h
    #ifndef MODULE_A_H
    #define MODULE_A_H
    extern "C" int foo( int x, int y );
    #endif
    ```
    在模块B的实现文件中仍然调用foo( 2,3 )，其结果是：
    （1）模块A编译生成foo的目标代码时，没有对其名字进行特殊处理，采用了C语言的方式；
    （2）连接器在为模块B的目标代码寻找foo(2,3)调用时，寻找的是未经修改的符号名_foo。
    如果在模块A中函数声明了foo为extern "C"类型，而模块B中包含的是extern int foo( int x, int y ) ，则模块B找不到模块A中的函数；反之亦然。
    所以，可以用一句话概括extern “C”这个声明的真实目的（任何语言中的任何语法特性的诞生都不是随意而为的，来源于真实世界的需求驱动。我们在思考问题时，不能只停留在这个语言是怎么做的，还要问一问它为什么要这么做，动机是什么，这样我们可以更深入地理解许多问题）：
    实现C++与C及其它语言的混合编程。
    明白了C++中extern "C"的设立动机，我们下面来具体分析extern "C"通常的使用技巧。
    extern "C"的惯用法
    （1）在C++中引用C语言中的函数和变量，在包含C语言头文件（假设为cExample.h）时，需进行下列处理：   
    ```
    extern "C"
    {
    #include "cExample.h"
    }
    ```
    而在C语言的头文件中，对其外部函数只能指定为extern类型，C语言中不支持extern "C"声明，在.c文件中包含了extern "C"时会出现编译语法错误。
    笔者编写的C++引用C函数例子工程中包含的三个文件的源代码如下：
    ```
    /*c语言头文件：cExample.h */
    #ifndef C_EXAMPLE_H
    #define C_EXAMPLE_H
    extern int add(int x,int y);
    #endif
    /*c语言实现文件：cExample.c */
    #include "cExample.h"
    int add( int x, int y )
    {
    return x + y;
    }
    //c++实现文件，调用add：cppFile.cpp
    extern "C"
    {
    #include "cExample.h"
    }
    int main(int argc, char* argv[])
    {
    add(2,3);
    return 0;
    }
    ```
    如果C++调用一个C语言编写的.DLL时，当包括.DLL的头文件或声明接口函数时，应加extern "C" {　}。
    （2）在C++引用C语言中的函数和变量时，C++的头文件需添加extern "C"，但是在C语言中不能直接引用声明了extern "C"的该头文件，应该仅将C文件中将C++中定义的extern "C"函数声明为extern类型。
    笔者编写的C引用C++函数例子工程中包含的三个文件的源代码如下：
    ```
    //C++头文件 cppExample.h
    #ifndef CPP_EXAMPLE_H
    #define CPP_EXAMPLE_H
    extern "C" int add( int x, int y );
    #endif
    //C++实现文件 cppExample.cpp
    #include "cppExample.h"
    int add( int x, int y )
    {
    return x + y;
    }
    /* C实现文件 cFile.c
    /* 这样会编译出错：#include "cppExample.h" */
    
    
    extern int add (int x,int y);
    int main (int argc,char*argv[])
    {
      add(2,3);
      return 0;
    }
    ```

参考：百度百科https://baike.baidu.com/item/extern/4443005?fr=aladdin

# 内存操作函数
## menset
   函数原型是：void *memset(void *s, int ch, size_t n);
   函数功能是：将s所指向的某一块内存中的前n个字节的内容全部设置为ch指定的ASCII值， 第一个值为指定的内存地址，块的大小由第三个参数指定，这个函数通常为新申请的内存做初始化工作， 其返回值为指向s的指针，它是对较大的结构体或数组进行清零操作的一种最快方法。  
   
   memset函数通常用来对一块已经分配地址的内存进行初始化，并且通常初始化为0或者字符'\0'（实际上是一样的）。下面是一些常见例子。



