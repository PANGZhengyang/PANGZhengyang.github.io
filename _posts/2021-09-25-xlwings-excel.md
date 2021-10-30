---
layout: post
title: "使用Xlwings刷Excel"
date: 2021-09-25
description: "使用Xlwings自动刷Excel。"
tag: Python
---

# 一、xlwings的基本对象
![xlwings基本对象](https://img-blog.csdnimg.cn/2f8d0c4719bf4143aac974e18b33f44c.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NjQzNTk0NA==,size_16,color_FFFFFF,t_70#pic_center)
Xlwings 的每个基本对象都有对应的api可以调用：
```python
import xlwings as xw

# app:
app = xw.App(visible = True, add_book = False)
app.quit()
# 刷新excel结束后执行之后的语句
app.api.CalculateUntilAsyncQueriesDone()

#book
wb = app.books.open(file_root)
wb = app.books.add()
wb = app.books.active

wb.save(file_root)
wb.close()
#刷新excel
wb.api.Refresh()

#sheet:
# 引用某指定sheet
sht=xw.books['工作簿名称'].sheets['sheet的名称']
# 激活sheet为活动工作表
sht.activate()
# 清除sheet的内容和格式
sht.clear()
# 清除sheet的内容
sht.clear_contents()
# 获取sheet的名称
sht.name
# 删除sheet
sht.delete

#range:
sht.range('A:C').value = 
# 加入超链接
# rng.add_hyperlink(r'www.baidu.com','百度',‘提示：点击即链接到百度')
# 取得当前range的地址
rng.address
rng.get_address()
# 清除range的内容
rng.clear_contents()
# 清除格式和内容
rng.clear()
# 设置range的颜色
rng.color=(255,255,255)
# 清除range的背景色
rng.color=None
# 获取公式或者输入公式
rng.formula='=SUM(B1:B5)'
rng.autofit()
# 所有列宽度自适应
rng.columns.autofit()
# 所有行宽度自适应
rng.rows.autofit()
```
其他的操作可以参考：[xlwings常用教程](https://blog.csdn.net/whalefall/article/details/102665002?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522162980749616780274111437%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=162980749616780274111437&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-102665002.first_rank_v2_pc_rank_v29&utm_term=xlwings&spm=1018.2226.3001.4187)
# 二、用xlwings 刷excel透视表
1.需要将基本sql代码写入excel数据源中
2.先刷数据源；在刷透视表
```python
import pandas as pd
import xlwings as xw
#input_file 是一个做好的excel模板
input_file = 'xxxx'
output_file = 'xxxx'
app = xw.App(visible = True,add_book = False)
wb = app.books.open(input_file)
#刷第一次：数据源
wb.api.RefreshAll()
app.api.CalculateUntilAsyncQueriesDone()
#刷第二次 透视表
wb.api.RefreshAll()
app.api.CalculateUntilAsyncQueriesDone()
wb.save(output_file)
wb.close()
app.quit()
```

# 三、xlwings与pandas、matplotlib相结合输出图
xlwings和pandas、matplotlib结合也是非常好的
1.执行sql 获取数据 转化成dataframe
2.可以填数据、可以画图，最后使用 wb.sheets['xx'].pictures.add(fig,top = 一个位置)
