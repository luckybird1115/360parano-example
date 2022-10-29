---
title: git常用命令
date: 2019-03-13 20:41:15
tags:
   - git
categories: 使用技巧
---
记录一些常用的git命令，目前理解还不是很深，有点进一步查阅资料
<!--more-->
1. 强制本地覆盖远程分支   
    ```
    git push origin *** --force  
    ```  
2. 放弃本地分支，远程分支强制覆盖本地
  
git fetch --all    //只是下载代码到本地，不进行合并操作
git reset --hard origin/分支名如master    //把HEAD指向最新下载的版本
3. 删除远程分支和本地分支
    ```
   git branch -a//查看分支
   git push origin --delete ***// 删除远程分支
   git branch -d ***//删除本地分支
   ```
4. git diff
    检查文件更改内容
5. 撤销未提交的修改git checkout
      ```
     git checkout app/src/main/../MainActivity 
     ```
6、git reset
   ```
   git log #找到需要退回的commit版本号，可以只复制前几位
   git reset <刚查找到的版本号>
   ```
7、git log


