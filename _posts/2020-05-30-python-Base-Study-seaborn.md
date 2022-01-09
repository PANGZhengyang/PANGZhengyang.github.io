---
layout: post
title: "Python Base Study——Seaborn"
date: 2020-05-30
description: "Python学习笔记"
tag: Python
---

This is my note for Python for Seaborn. When I face some tricky problems, I'll record them.
Let's start!

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline

import warnings
warnings.filterwarnings('ignore') # 不发出警告

plt.rcParams['font.sans-serif'] = ['Microsoft YaHei']#解决中文乱码
plt.rcParams['axes.unicode_minus'] = False #解决负号无法显示

#设置基调 风格选择包括："white", "dark", "whitegrid", "darkgrid", "ticks"
sns.set_style('darkgrid')
#设置显示比例尺度：'paper', 'notebook', 'talk', 'poster'
sns.set_context('notebook')
```

设置局部格式

```python
# axes_style() 设置局部图表风格，可学习和with配合的用法
'''
with sns.axes_style("darkgrid"):
    作图

'''
# 设置图表坐标轴 sns.despine(fig=None, ax=None, top=True, right=True, left=False, bottom=False, offset=None, trim=False)
```

颜色盘

```python
sns.color_palette()
# 默认6种颜色：deep, muted, pastel, bright, dark, colorblind
# sns.color_palette(palette=None, n_colors=None, desat=None)
#设置调色板后，绘图创建图表
```

举例

```python
iris = sns.load_dataset('iris')
iris.head(2)

# 1.变量分布可用于表达一组数值的分布趋势，包括集中程度、离散程度等
#单变量分布：sns.distplot()
fig, ax = plt.subplots(1,2,figsize = (6,4))
sns.distplot(iris['sepal_length'],
             hist_kws={"linewidth": 1,"alpha": 0.8, "color": "g"},
             kde_kws ={'color':'r','linestyle' : '--'},
             ax = ax[0])

#双变量分布：
'''
joint意为联合，顾名思义jointplot是一个双变量分布图表接口。
绘图结果主要有三部分：绘图主体用于表达两个变量对应的散点图分布，在其上侧和右侧分别体现2个变量的直方图分布：
'''
sns.jointplot(x=df['A'], y=df['B'],  # 设置xy轴，显示columns名称
              data=df,   # 设置数据
              color = 'k',   # 设置颜色
              s = 50, edgecolor="w",linewidth=1,  # 设置散点大小、边缘线颜色及宽度(只针对scatter）
              kind = 'scatter',   # 设置类型：“scatter”、“reg”、“resid”、“kde”、“hex”
              space = 0.2,  # 设置散点图和布局图的间距
              size = 8,   # 图表大小（自动调整为正方形）
              ratio = 5,  # 散点图与布局图高度比，整型
              #marginal_kws=dict(bins=15, rug=True)  # 设置柱状图箱数，是否设置rug
              )  

#自定义jointplot可以使用JointGrid
# 可拆分绘制的散点图
# plot_joint() + ax_marg_x.hist() + ax_marg_y.hist() ---只是hist
# plot_joint() + plot_marginals(sns.distplot, kde=True, color="g") ---可以改成distplot

g = sns.JointGrid(x="total_bill", y="tip", data=tips)
# 创建一个绘图表格区域，设置好x、y对应数据

g.plot_joint(plt.scatter, color ='m', edgecolor = 'white')  # 设置框内图表，scatter
g.ax_marg_x.hist(tips["total_bill"], color="b", alpha=.6,
                 bins=np.arange(0, 60, 3))            # 设置x轴直方图，注意bins是数组
g.ax_marg_y.hist(tips["tip"], color="r", alpha=.6,
                 orientation="horizontal",
                 bins=np.arange(0, 12, 1))            # 设置x轴直方图，注意需要orientation参数

#多变量分布：
'''
pairplot() 当变量数不止2个时，pairplot是查看各变量间分布关系的首选。
它将变量的任意两两组合分布绘制成一个子图，对角线用直方图、而其余子图用相应变量分别作为x、y轴绘制散点图。
'''
sns.pairplot(iris,
            kind = 'scatter',  # 散点图/回归分布图 {‘scatter’, ‘reg’}  
            diag_kind="hist",  # 直方图/密度图 {‘hist’, ‘kde’}
            hue="species",   # 按照某一字段进行分类
            palette="husl",  # 设置调色板
            markers=["o", "s", "D"],  # 设置不同系列的点样式（这里根据参考分类个数）
            size = 2,   # 图表大小
            )

#自定义pairplot可以使用PairGrid
# 可拆分绘制的散点图
# map_diag() + map_lower() + map_upper() / map_diag()+map_offdiag()

g = sns.PairGrid(iris,hue="species",palette = 'hls',
                vars = ['sepal_length','sepal_width','petal_length','petal_width'],  # 可筛选
                )
# 创建一个绘图表格区域，设置好x、y对应数据，按照species分类

g.map_diag(plt.hist, 
           histtype = 'barstacked',   # 可选：'bar', 'barstacked', 'step', 'stepfilled'
           linewidth = 1, edgecolor = 'w')           
# 对角线图表，plt.hist/sns.kdeplot
g.map_upper(plt.scatter, color = 'r')     # 设置对角线上端图表
g.map_lower(sns.kdeplot, cmap="Blues_d")  # 设置对角线下端图表
```

# 分类数据
## 散点图（用的不多）
分类数据散点图接口主要用于当一列数据是分类变量时。相比于两列数据均为数值型数据，可以想象分类数据的散点图将会是多条竖直的散点线。绘图接口有
stripplot和swarmplot两种，常用参数是一致的，主要包括：
      
    x，散点图的x轴数据，一般为分类型数据
    y，散点图的y轴数据，一般为数值型数据
    hue，区分维度，相当于增加了第三个参数
    data，pandas.dataframe对象，以上几个参数一般为data中的某一列

***1.stripplot***
    
     常规的散点图接口，可通过jitter参数开启散点左右"抖动"效果（实际即为在水平方向上加了一个随机数控制x坐标，默认jitter=True；
     当设jitter  为False时，散点图均严格位于一条直线上

```py
sns.stripplot(x="day",          # x → 设置分组统计字段
              y="total_bill",   # y → 数据分布统计字段
              # 这里xy数据对调，将会使得散点图横向分布
              data=tips,        # data → 对应数据
              hue="day",        # hue - 再分类
              jitter = True,    # jitter → 当点数据重合较多时，用该参数做一些调整，也可以设置间距如：jitter = 0.1
              size = 5, edgecolor = 'w',linewidth=1,marker = 'o'  # 设置点的大小、描边颜色或宽度、点样式
              palette="Set2",  # 设置调色盘
              dodge=True,  # 是否拆分
              order = ['Sat','Sun']   # order → 筛选类别
              )
```

***2.swapplot***

    在stripplot的基础上，不仅将散点图通过抖动来实现相对分离，而且会严格讲各散点一字排开，从而便于直观观察散点的分布聚集情况：

## 分布图
***1.boxplot*** 

```py
    sns.boxplot(x="day", y="total_bill", data=tips,
            linewidth = 2,   # 线宽
            width = 0.8,     # 箱之间的间隔比例
            hue = 'smoker'   # 再分类
            fliersize = 3,   # 异常点大小
            palette = 'hls', # 设置调色板
            whis = 1.5,      # 设置IQR 
            order = ['Thur','Fri','Sat','Sun'],  # 筛选类别
           )
```
***2.violinplot***
```py
    sns.violinplot(x="day", y="total_bill", data=tips,
            linewidth = 2,   # 线宽
            width = 0.8,     # 箱之间的间隔比例
            hue = 'smoker'   # 再分类
            palette = 'hls', # 设置调色板
            order = ['Thur','Fri','Sat','Sun'],  # 筛选类别
            scale = 'area',  # 测度小提琴图的宽度：area-面积相同，count-按照样本数量决定宽度，width-宽度一样
            inner = 'box',   # 设置内部显示类型 → “box”, “quartile”, “point”, “stick”, None
           )
```
## 统计图

***1.barplot***
柱状图 - 置信区间估计
置信区间：样本均值 + 抽样误差
```py
   sns.barplot(x="sex", y="survived", hue="class", data=titanic,
            palette = 'hls', 
            order = ['male','female'],  # 筛选类别
            capsize = 0.05,  # 误差线横向延伸宽度
            saturation=.8,   # 颜色饱和度
            errcolor = 'gray',errwidth = 2,  # 误差线颜色，宽度
            ci = 'sd'    # 置信区间误差 → 0-100内值、'sd'、None
            )
```
***2.countplot 计数图***
```py
sns.countplot(x="class", hue="who", data=titanic,palette = 'magma')
#sns.countplot(y="class", hue="who", data=titanic,palette = 'magma')  
# x/y → 以x或者y轴绘图（横向，竖向）
```
***3.pointplot*** 如果x是连续的时间用这个图

## 线性关系

```python
#线性关系数据
sns.lmplot(x="size", y="total_bill", hue="day", col="day",data=tips, 
           aspect=0.6,    # 长宽比
           x_jitter=.30,  # 给x或者y轴随机增加噪音点
           col_wrap=4,    # 每行的列数
          )
```

## 热力图

```python
#热力图
cmap = sns.diverging_palette(220, 10, as_cmap=True) #用调色板调颜色 as_cmap 
sns.heatmap(iris.corr(),cmap = cmap,vmax=.3, center=0,square=True, linewidths=0.2)
```

