---
layout: post
title: "Python Base Study——Matplotlib"
date: 2020-05-27
description: "Python学习笔记"
tag: Python
---

This is my note for Python for Matplotlib. When I face some tricky problems, I'll record them.
Let's start!

首先导入所需要的包：

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import warnings
warnings.filterwarnings('ignore') # 不发出警告


#魔法函数
%matplotlib inline

#解决中文乱码
plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']

#解决负号无法显示
plt.rcParams['axes.unicode_minus'] = False 

```

采用面向对象的方法：

```python
x = np.arange(6)
y = np.arange(6)
fig, ax = plt.subplots(figsize=(10,8))
ax.plot(x,y,label ='a',)
ax.bar(x,y,label = 'b',color = 'r')

#color 可以调用sns.color_palette('Paired')

#ax.set_title(self, label, fontdict=None, loc=None, pad=None,  **kwargs) loc：位置 pad：title距离顶线多远
ax.set_title(label='pzy',fontdict =font1,loc = 'left',pad = 10) 

#坐标轴的名称
#ax.set_xlabel(xlabel, fontdict=None, labelpad=None, loc=None, **kwargs)
#ax.set_ylabel(ylabel, fontdict=None, labelpad=None, loc=None, **kwargs)
ax.set_xlabel('time',fontdict = font1 )
ax.set_ylabel('value',fontsize = 22,c = 'r')

#坐标轴的刻度
# axes.set_xticks(ticks,minor = False) / axex.set_yticks(ticks,minor = False)
# ticks:以列表形式传入轴的刻度 minor：是否展示更小级别的刻度
#隐藏刻度传入空列表
ax.set_xticks([0,2,4,6])
ax.set_yticks([0,3,6,9])

#刻度标签 添加标签
#axes.set_xticklabels(self, labels, fontdict=None, minor=False, **kwargs)
#label: 刻度标签列表

#ax.set_xticklabels(['one','two','three','four'],rotation = 40)

#坐标轴范围:设置范围
#axes.set_xlim(self, left=None, right=None, emit=True, auto=False, *, xmin=None, xmax=None)
#axes.set_ylim(self, bottom=None, top=None, emit=True, auto=False, *, ymin=None, ymax=None)
ax.set_xlim(0,8)
ax.set_ylim(-1,8)

#图例
#ax.legend(loc,fontdict)
ax.legend(loc = 0,fontsize = 20)

#网格线
#axes.grid(self, b=None, which=‘major’, axis=‘both’, linestyle = '--',color = *,linewidth = ,*kwargs)
'''b：布尔值。选择是否展示网格线，如果配置了**kwargs则默认为展示网格线。
which：可选{‘major’, ‘minor’, ‘both’}。选择要修改的网格线。
axis：可选{‘both’, ‘x’, ‘y’}。选择要修改的轴线
**kwargs : .Line2D properties。线条的设置，如颜色，样式，粗细等'''
ax.grid(axis = 'x',linestyle = '--',linewidth=2)
```

不同种类的图形

```python
fig,ax = plt.subplots(4,figsize=(10,10))

#折线图 plot(x轴坐标，y轴坐标，fmt:[marker],[line],[color], scalex=True, scaley=True, data=None, **kwargs)
'''
scalex：x轴数据标签是否自动适应数据，默认True
scaley：y轴数据标签是否自动适应数据，默认True

data：当有一个表格时，可以选取其中部分列作为图的x,y轴

**kwargs：其他关键字参数
'''
x = [1,2,3,4,5,6]
y = [1,4,9,16,25,36]
ax[0].plot(x,y,'--g.')

#条形图 bar(x, height, width=0.8, bottom=None, facecolor=,edgcolor=, align=‘center’, data=None, **kwargs)
# x,y参数：x，y值
# width：宽度比例
# facecolor柱状图里填充的颜色、edgecolor是边框的颜色
# left-每个柱x轴左边界,bottom-每个柱y轴下边界 → bottom扩展即可化为甘特图 Gantt Chart
# align：决定整个bar图分布，默认left表示默认从左边界开始绘制,center会将图绘制在中间位置
# xerr/yerr ：x/y方向error bar
x = np.arange(10)
y1 = np.random.rand(10)
ax[1].bar(x,y1,width = 1,facecolor = 'yellowgreen',edgecolor = 'white',yerr = y1*0.1)
ax[1].bar(x,y1,color='b',width = 1,bottom = y1)


#饼图 plt.pie(x, explode=None, labels=None, colors=None, autopct=None, pctdistance=0.6, shadow=False, labeldistance=1.1, startangle=None, 
# radius=None, counterclock=True, wedgeprops=None, textprops=None, center=(0, 0), frame=False, hold=None, data=None)
s = pd.Series(3 * np.random.rand(4), index=['a', 'b', 'c', 'd'], name='series')
ax[2].axis('equal')  # 保证长宽相等
ax[2].pie(s,
       explode = [0.1,0,0,0],
       labels = s.index,
       colors=['r', 'g', 'b', 'c'],
       autopct='%.2f%%',
       pctdistance=0.6,
       labeldistance = 1.2,
       shadow = True,
       startangle=0,
       radius=1.5,
       frame=False)
# 第一个参数：数据
# explode：指定每部分的偏移量
# labels：标签
# colors：颜色
# autopct：饼图上的数据标签显示方式
# pctdistance：每个饼切片的中心和通过autopct生成的文本开始之间的比例
# labeldistance：被画饼标记的直径,默认值：1.1
# shadow：阴影
# startangle：开始角度
# radius：半径
# frame：图框
# counterclock：指定指针方向，顺时针或者逆时针

#直方图.hist(x, bins=10, range=None, normed=False, weights=None, cumulative=False, bottom=None, 
#histtype='bar', align='mid', orientation='vertical',rwidth=None, log=False, color=None, label=None, 
#stacked=False, hold=None, data=None, **kwargs)

s = pd.Series(np.random.randn(1000))
ax[3].hist(s,
        bins = 20,
       histtype = 'bar',
       align = 'mid',
       orientation = 'vertical',
       alpha=0.5,
       )
'''
x：一个数组
bins：直方图间隔。可以为int，str，序列。如果为整数则代表切割的分数
range：x轴的范围，一个2元素元组。超过range范围的x数组中的值，视为异常值舍弃。如果bins为序列，则范围无影响。
density：当为True时，按频率统计。
weights：权重。
cumulative：为True时，计算累计频数。
bottom：以为直方图的每个条形添加基准线，默认为0；
histtype：图像绘制类型{‘bar’, ‘barstacked’, ‘step’, ‘stepfilled’}
align：对齐方式{‘left’, ‘mid’, ‘right’}
orientation：箱体的方向{‘horizontal’, ‘vertical’}
rwidth：箱体的宽度与bins宽度的比值。如bins宽为2，rwidth=0.8，则柱子的宽度为1.6，柱子的间隔为0.4
log：当为True时，则返回对数刻度。
color：颜色
label：刻度标签
stackde：当为True时，图像为堆积图。
'''

#散点图 .scatter(x, y, s=20, c=None, marker='o', cmap=None, norm=None, vmin=None, vmax=None, 
# alpha=None, linewidths=None, verts=None, edgecolors=None, hold=None, data=None, **kwargs)

plt.figure(figsize=(8,6))
x = np.random.randn(1000)
y = np.random.randn(1000)
plt.scatter(x,y,marker='.',
           s = np.random.randn(1000)*100,
           cmap = 'Reds',
           c = y,
           alpha = 0.8,)
plt.grid()
# s：散点的大小
# c：散点的颜色
# vmin,vmax：亮度设置，标量
# cmap：colormap
```

设置数据标签

```python
#设置数据标签 axes.text(self, x, y, s, fontdict=None, withdash=, **kwargs)
'''x / y：图中的x,y轴坐标
s：文本
fontdict：文本属性字典
verticalalignment (va)：水平对齐方式 ，可选{‘center’ , ‘top’ , ‘bottom’ ,‘baseline’ }
horizontalalignment (ha)：垂直对齐方式，可选{‘left’, ‘right’, ‘center’}
**kwargs：其他关键字参数

    rotation：旋转角度，可选{‘vertical‘, ‘horizontal’} 。也可是其他角度
    alpha：透明度。
    color：标题文字颜色
    backgroundcolor：标题背景颜色
    bbox：参数字典。给标题增加外框 ，常用参数：
        alpha：透明度
        boxstyle：方框外形，可选{‘circle’, ‘darrow’, ‘larrow’, ‘rarrow’, ‘round’ ,'round4 ', ‘roundtooth’, ‘sawtooth’, ‘square’}
        facecolor(fc)：背景颜色
        edgecolor(ec)：边框线条颜色
        edgewidth：边框线条大小
        pad：文本与文本框的距离
'''
x = np.arange(6)
y = np.arange(6)
z = np.arange(1,7)

plt.plot(x,y,marker = '.')
for a,b in zip(x,y):
    plt.text(a-0.3,b+0.3,(a,b),fontsize = 10,c = 'r')

#对于bar 图 没有直接显示值得字段所以用plt.text
'''
for x,y in enumerate(df['某个字段']):
    plt.text(x,y+0.2,"%s"%round(y,1),ha='center'))
'''
```

设置辅助线

```python
#辅助线
#plt.axvline(key_num,color='r',linestyle="--",alpha=0.8) 
#plt.axhline()
```

设置标注

```python
# plt.annotate
'''
    plt.annotate(  
        'text',  
        xy=(70, 1), arrowprops=dict(arrowstyle='->'), xytext=(15, -10))

xy 是被指向的位置
xytext：是文字的位置
arrowprops=dict(arrowstyle ='->',color = 'r')
'''

```







