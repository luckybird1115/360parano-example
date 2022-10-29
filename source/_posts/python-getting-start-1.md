---
title: python入门
date: 2020-10-06 16:31:16
tags: python
categories: python
---
python 安装使用tesseract，完成验证码图像识别；使用ssenium下载Chromedriver 模拟浏览器
<!--more-->

### pytesseract/tesseror

tesseract 下载地址：https://digi.bib.uni-mannheim.de/tesseract/

遇到的问题:

1.FileNotFoundError: [WinError 2] 系统找不到指定的文件

解决方法:

方法1[推荐]: 将tesseract.exe添加到环境变量PATH中，

例如: D:\Tesseract-OCR,默认路径为C:\Program Files (x86)\Tesseract-OCR

注意: 为了使环境变量生效，需要关闭cmd窗口或是关闭pycharm等ide重新启动

方法2: 修改pytesseract.py文件，指定tesseract.exe安装路径

```
# CHANGE THIS IF TESSERACT IS NOT IN YOUR PATH, OR IS NAMED DIFFERENTLY
tesseract_cmd = 'C:\\Program Files (x86)\\Tesseract-OCR\\tesseract.exe‘
```

方法3:  在实际运行代码中指定

```
pytesseract.pytesseract.tesseract_cmd = 'D:\\Tesseract-OCR\\tesseract.exe'
```

 

2.pytesseract.pytesseract.TesseractError: (1, 'Error opening data file \\Tesseract-OCR\\tessdata/eng.traineddata')

 解决方法:

方法1[推荐]: 

将tessdata目录的上级目录所在路径(默认为tesseract-ocr安装目录)添加至TESSDATA_PREFIX环境变量中

例如: C:\Program Files (x86)\Tesseract-OCR

```
Please make sure the TESSDATA_PREFIX environment variable is set to the parent directory of your "tessdata" directory.
```

 

方法2:  在.py文件配置中指定tessdata-dir

```
tessdata_dir_config = '--tessdata-dir "D:\\Tesseract-OCR\\tessdata"'
# tessdata_dir_config = '--tessdata-dir "'C:\\Program Files (x86)\\Tesseract-OCR\\tessdata"'
pytesseract.image_to_string(image, config=tessdata_dir_config)
```

### sessnium

#### 环境安装

```
python+selenium+pycharm+webdriver 
1、 python安装包:https://www.python.org/getit/ 
2、 PyCharm 安装包:http://www.jetbrains.com/pycharm/download/ 
3、 Selenium安装包:https://pypi.python.org/pypi/selenium 
     selenium安装方式一: 安装python包，选择全部组件(pip、安装过程中配置环境变量) 然后执行pip install -U selenium 联网安装Selenium; 
     安装方式二: 安装python包，选择全部组件(pip、安装过程中配置环境变量) 解压selenium-3.13.0.tar.gz，然后用cmd进入解压目录，使用命令 Python setup.py install 安装Selenium。 
4、 webdriver配置(以chromedriver为例): 
Chromedriver下载地址：http://npm.taobao.org/mirrors/chromedriver/ ***版本与浏览器版本保持一致
 
配置方式一: 1)把下载好的chromedriver.exe程序放置到python的安装路径下 2)在python中代码编写如下即可:
driver = webdriver.Chrome() # Firefox、Ie、Edge等
配置方式二: 1)把下载好的chromedriver.exe程序放置到python项目中(其它路径也可) 2)在python中代码编写如下即可:
chromePath = chromedriver.exe路径 os.environ[‘webdriver.chrome.driver’] = chromePath # gecko ie等
driver = webdriver.Chrome(executable_path=chromePath) # Firefox、Ie等
```

安装后需要，下载对应的Chromedriver驱动https://sites.google.com/a/chromium.org/chromedriver/downloads/version-selection

使用webDriver API

https://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.common.action_chains

#### 识别元素

**三、识别元素**

1. 基本元素定位

   driver.find_element_by_id(``'id_value'``).send_keys(``'最新疫情咨讯'``)  #通过id定位``driver.find_element_by_name(``'name_value'``).send_keys(``'最新疫情咨讯'``)  #通过name定位``driver.find_element_by_class_name(``'class_name_value'``).send_keys(``'最新疫情信息'``)  #通过``class``元素定位

   driver.find_element_by_tag_name(``'input'``).send_key(``'最新疫情咨讯'``)  #通过tag_name定位，<缺点：tag_name值不唯一>

   ``driver.find_element_by_link_text(``'新闻'``).click()   #link_text链接中的文本  <缺点：只对标签生效>``

   driver.find_element_by_partial_link_text(``'新'``).click()   #partial_link_text部分链接中的文本  <缺点：只对标签生效>

2. xpath定位元素

### 基本爬取下载数据