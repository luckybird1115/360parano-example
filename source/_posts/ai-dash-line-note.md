---
title: ai组件开发--虚线定义原理及编程实现
date: 2019-03-25 15:41:27
tags:
  - AI  
categories: AI组件开发
---
illustrator中定义虚线形式可以最多设置三个虚线长度和三个间隙长度
<!--more-->
# 虚线定义
illustrator中定义虚线形式可以最多设置三个虚线长度和三个间隙长度  
__需要特别注意如果定义奇数个参数，即没有定义spaceA，spaceB，spaceC时的情况,其中spaceB和spaceC的循环情况原理一致
依次标记为dashA,spaceA,dashB,spaceB,dashC,spaceC
为更明显的展示，定义：  
dashA: 3
spaceA: 5

dashB: 4
spaceB:6

dashC: 2
spaceC: 1
虚线：—表示一个pt
间隙：#表示一个pt
结果形式：展示两次循环，每次循环间为区分明显添加了一个空格
下面简单介绍定义规则
1. 设置dashA，其他值为空  
   一次循环：此时按照dashA的长度绘制实部，此时，illustrator默认spaceA=dashA，因此间隙也是dashA的长度  
   结果形式:———### ———###  
2. 设置dashA，spaceA,其他值为空  
   一次循环：此时按照dashA的长度绘制实部，间隙是spaceA的长度  
   结果形式:———##### ———#####
3. 设置dashA，spaceA,dashB,其他值为空，__需注意__ 
   一次循环：此时按照dashA的长度绘制第一个实部，间隙是spaceA的长度,按照dashB的长度绘制第二个实部，按照dashA绘制间隙，按照spaceA绘制第三个实部，按照dashB绘制间隙    
   结果形式:———#####————####—————#### ———#####————####—————####
4. 设置dashA，spaceA,dashB,spaceB,其他值为空
    一次循环：此时按照dashA的长度绘制第一个实部，间隙是spaceA的长度,按照dashB的长度绘制第二个实部，间隙是spaceB的长度,
    结果形式:———#####————###### ———#####————######
5. 设置dashA，spaceA,dashB,spaceB,dashC,其他值为空
    一次循环：此时按照dashA的长度绘制第一个实部，间隙是spaceA的长度,按照dashB的长度绘制第二个实部，间隙是spaceB的长度,按照dashC的长度绘制第三个实部,间隙是spaceC的长度，按照dashA绘制间隙，按照spaceA绘制第四个实部，按照dashB绘制间隙，按照spaceB绘制第五个实部，按照dashC绘制间隙,按照spaceC绘制第六个实部    
    结果形式:———#####————######——###—————####——————## ———#####————######——###—————####——————##
6. 设置dashA，spaceA,dashB,spaceB,dashC,spaceC 
    原理同4一致，不再赘述
 
__概括来说，AI本身每次循环使用六个参数定义虚线，如果不足就会重复前面定义的参数__
# illustrator SDK接口实现
