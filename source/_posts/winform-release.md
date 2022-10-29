---
title: VS2013打包C#项目
date: 2019-03-22 21:54:22
tags: 
    - VS
    - 打包
categories: 应用
---
在VS2013中安装InstallShield打包程序，
<!--more-->
# 背景
vs2010打包时，在其他项目类型中找到安装和部署的模板，即可新建安装项目，而在vs2013中需要先在线搜索InstallShield，然后按照网页提示一步步下载，之后添加安装项目，配置打包设置完成打包
 在vs2010 选择“新建项目”→“ 其他项目类型”→“ Visual Studio Installer→“安装项目”：
# 1. 打包前的准备
## InstallShield安装
在我们准备好的项目中右击“解决方案”，在弹出的菜单中选择添加 - 新建项目：
 
在弹出的新建对话框中依次选择 其他项目 - 安装和部署 - 安装项目，你可以改名字，也可以使用默认的名字setup1

新建一个打包部署项目，点OK，如果是第一次使用的话，会打开一个网页，按照提示的步骤来做，

点击__Go to the download web site__,进入下载网站，填写完信息，点击“download ”即会收到一封邮件，里面有下载地址和激活码。

之后会下载一个__InstallShield2015LimitedEdition.exe__的安装包,用管理员权限运行安装完后。重新启动VS2013

重新新建一个这样的项目：

点OK，这时候不再是打开网页，这时打开会提示激活或者是试用，选第一项，下一步

输入收到的邮件里的激活码，激活,待激活完成

卸载功能：找到C:\Windows\System32目录下的msiexec.exe文件，右击 选择 “msiexec.exe 的快捷方式”，然后改名为：你想叫名称（卸载、uninstall都可以），移动到你需要打包的程序菜单的文件夹下面。
## winform设置项目图标
   - 首先可以在你的窗体属性中找到icon选择你准备好的图标；
   - 右键项目点击属性，找到应用程序中的__图标和清单__


# 2. 开始部署
## 2.1 基本配置Application Information
   可以设置公司名称，点击右侧General Information 可以设置语言为中文，可以设置应用名称
## 2.2 Installation Requirements
   一般可以默认设置，如果需要,选择安装如.NET Framework 4.5等依赖同时打包，勾选所需依赖
## 2.3 Installation Architecture
   默认即可
## 2.4 Application Files
   添加程序所需文件，这里有两种方式经过测试均可实现
### 2.4.1方式一
   点击到ProgramFilesFolder>Microsoft> <你的工程名>[INSRALLDIR]，点击__Add Folders__，选择工程bin目录下的Debug或Release文件夹，一般选择Release
### 2.4.2 方式二
   点击到ProgramFilesFolder>Microsoft> <你的工程名>[INSRALLDIR]，点击__Add Project Outputs__,选择主输出
   
   点击Ok后，选中主输出，右键Resolve Project OutPut
   
   点击OK后，在左边菜单CatcheScreen下新建目录source，然后同上步骤添加源文件，然后选中资源，右键Resolve Project OutPut
   
   在创建一个文件命名“uninstall”，然后同上步骤添加源文件，然后选中资源，右键Resolve Project OutPut
## 2.5 Application Shortcuts
   + 2.4如果选择方式一，这是默认后有两个Launch xxx.exe,Launch xxx.vshost.exe,可以将第二个删掉，然后重命名第一个
      勾选use alternate shortcut icon可以选择自定义的程序图标
      然后创建卸载程序的快捷方式
      点击Create an unistallation shortcut，选择uninstall
   + 2.4如果选择方式二，  
       点击左侧的new,弹出对话框后点击最后一项，一直点到最后,点击Open，重命名Built（这里的重命名实际上就是你的程序在桌面上的名称) 选择在桌面上创建快捷方式：
      勾选use alternate shortcut icon可以选择自定义的程序图标
           然后创建卸载程序的快捷方式
## 2.6  Application Registry
   配置是否允许用户修改安装地址等
## 2.7 在右侧找到第六步Prepare for Release，双击下面的Releases
  点击选中“SingleImage”–选项卡点击“Setup.exe”–找到 “InstallShield PrerequisitesLocation”，把它设置为“Extract From Setup.exe”   
# 发布
  生成解决方案后会在安装程序集下面生成一个名为Setup（当时添加安装部署项目的项目名）的文件夹，安装文件就保存在下面的目录中。Install在生成安装文件后会有Setup.exe和.msi两中安装文件，exe文件是安装的引导文件，核心文件是msi文件，里面封存了程序的组件。在里面找到Setup.exe文件及msi文件即可进行安装。

 点击Setup.exe，进行安装，如果想卸载，点击Uninstall卸载
# 更换用installshield打包生成exe文件的图标
  - 打开Visio studio,选择文件方式打开你生成的exe文件
  - 在Icon下右键添加资源》点击导入选择准备好的项目图标等
  - 删除原来的100资源
  - 设置你导入的资源ID为100
  - 保存
# 常见问题
  1. 有时打包之后，项目更新之后，重新再生成解决方案来打包,遇到exe文件找不到时  
      【解决方案】
      + 清理，重新生成解决方案
      + 如果还是不行，运行一次，然后点击生成解决方案
  2. 重新打包遇到DVD5无法覆盖  
      【解决方案】原因是通过打开了相关文件夹，关闭文件资源管理器即可
  3. 设置了应用的ico图标后，生成解决方案提示 error : -3204: Cannot extract icon with index 0 from file...
      【解决方案】把图标添加进工程。
  4. 重新打包遇到错误-1014：     
     错误	29	-1014: Cannot rename directory XXX\Express\SingleImage to XXX\Express\SingleImage.Bak. 
     Windows Explorer or a DOS prompt may be pointing to a subfolder of the release output folder (Disk1) or to the Interm folder, locking it. Change the current directory. Close any open files in the Disk1 folder. Close Msidb.exe if it is open.  
    【解决方案】将工程文件夹下的当时创建的SetUp应用程序中SetUp（当初新建的工程名）的文件夹删除，保留,isl，isproj文件，点击 生成解决方案即可，在XXX\Express\SingleImage\DiskImages\DISK1下生成可执行文件
      
参考：https://blog.csdn.net/qq_38122230/article/details/80470699
https://blog.csdn.net/u010872301/article/details/80509405
