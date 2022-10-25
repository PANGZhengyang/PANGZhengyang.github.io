---
layout: post
title: "Selenium"
date: 2022-09-10
description: "Python学习笔记"
tag: Python
---

This is my note for Python for Selenium. When I face some tricky problems, I'll record them.
Let's start!

Selenium是一个自动化测试框架，在使用streamlit搭建看板时，业务方反馈有些数据手动刷新太卡太慢，故想去实现一个自动刷新的功能。刚好看到这个自动化框架，浅试一下，了解一些这个包的基础内容。

##  安装与导入

前期需要安装三个东西：

- 浏览器：Chrome、FireFox

- 浏览器的驱动程序：为了让浏览器和Selenium产生关联，需要下载驱动：[chromedrive](https://blog.csdn.net/zhoukeguai/article/details/113247342)，将浏览器驱动放在浏览器同级目录下

- Selenium： `pip install --user selenium`

## 与浏览器建立连接

```python
from selenium import webdriver

c=webdriver.Chrome(executable_path=r'./chromedriver.exe') # 获取chrome浏览器的驱动，并启动Chrome浏览器
c.get('https://www.baidu.com')# 打开百度
```

## 查找元素

对网页进行操作，需要去找到相应的元素，然后做出下一步操作。

```python
from selenium.webdriver.common.by import By

c.find_element(By.ID, 'kw')
c.find_element(By.XPATH, r'')
```

查找元素可以使用：在网页中点击右键出现检查或者在网页中使用F12。

## 浏览器操作

这一块主要是对浏览器进行操作

```python
##窗口操作
c.maximize_window()#最大化
c.fullscreen_window() #全屏
c.minimize_window() #最小化
c.get_window_position() #获取窗口的坐标
c.get_window_rect()#获取窗口的大小和坐标
c.get_window_size()#获取窗口的大小
c.set_window_position(100,200)#设置窗口的坐标
c.set_window_rect(100,200,32,50)    #设置窗口的大小和坐标
c.set_window_size(400,600)#设置窗口的大小
c.current_window_handle   #返回当前窗口的句柄
c.window_handles         #返回当前会话中的所有窗口的句柄

## 关闭
c.close() # 关闭当前标签页
c.quit() # 关闭浏览器并关闭驱动

## 截屏
c.save_screenshot('1.png') # 截图，只支持PNG格式
c.get_screenshot_as_png() # 获取当前窗口的截图作为二进制数据
c.get_screenshot_as_base64() # 获取当前窗口的截图作为base64编码的字符串

# 前进后退刷新
c.forward() #前进
c.back()  #后退
c.refresh()#刷新

```

## 元素操作

对我们找到的元素进行二次操作，不仅可以再次选择子元素还可以进行其它操作。

```python
kw1.clear()        #清除元素的值
kw1.click()        #点击元素
kw1.id             #Selenium所使用的内部ID
kw1.get_property('background') #获取元素的属性的值
kw1.get_attribute('id') #获取元素的属性的值
kw1.location       #不滚动获取元素的坐标
kw1.location_once_scrolled_into_view  #不滚动且底部对齐并获取元素的坐标
kw1.parent         #父元素
kw1.send_keys('')  #向元素内输入值
kw1.size           #大小
kw1.submit         #提交
kw1.screenshot('2.png') #截取元素形状并保存为图片
kw1.tag_name       #标签名
kw1.text           #内容，如果是表单元素则无法获取
kw1.is_selected()  #判断元素是否被选中
kw1.is_enabled()   #判断元素是否可编辑
kw1.is_displayed   #判断元素是否显示
kw1.value_of_css_property('color') #获取元素属性的值
kw1._upload('2.png') #上传文件
```

------

Selenium的内容也比较多，在需要些比较难的操作时，可以查询文档。

[Selenium教程](https://blog.csdn.net/qq_44326412/article/details/107825851?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-107825851-blog-104636611.pc_relevant_multi_platform_whitelistv3&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-107825851-blog-104636611.pc_relevant_multi_platform_whitelistv3&utm_relevant_index=1)

[Selenium手册](https://www.selenium.dev/zh-cn/documentation/webdriver/getting_started/install_drivers/)