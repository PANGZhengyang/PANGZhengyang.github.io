---

layout: post
title: "使用python实现假设检验"
date: 2021-03-21
description: "用python实现假设检验"
tag: Python
katex: true
---

Python 中的假设检验一般用到 scipy 或 statsmodels 包，需要注意的是，这两个包里面各种检验的显著性水平都是 0.05。

## z-test

在`statsmodels`包中有`ztest`方法：

```python
ztest(x1,x2,value=0,alternative='two-sided')
x1:数组，第一个样本的各数据值
x2:数组，第二个样本的各数据值
value: 若是单样本，value是假设的总体均值；若为双样本，value是总体均值间的差值
alternative: 'two-sided' 双侧 'larger' 右侧  'smaller' 左侧：H1:value<0
```

```pyth
import numpy as np
import statsmodels.stats.weightstats as sw

#单样本z-test
arr1 = np.array([650, 730, 510, 670, 480, 800, 690, 530, 
590, 620, 710, 670, 640, 780, 650, 490, 800, 600, 510, 700])
sw.ztest(arr1,value=690)

#双样本z-test
arr2 = np.array([510, 670, 480, 800, 690, 530, 590, 620, 
710, 670, 640, 780, 650, 490, 800, 600,700,600,800,490,500,690,680,670,710])
sw.ztest(arr1,arr2,value=0) #H0：总体1的均值-总体2的均值=0
```

对于比例的检验，通常使用z-test

```python
from statsmodels.stats.proportion import proportions_ztest
proportion_ztest(count，nobs，value = None，Alternative =‘two-side’)
```

```python
from statsmodels.stats.proportion import proportions_ztest 

z_score, p_value = proportions_ztest([3124, 1598], [80723, 80689], alternative='two-sided')
print('p_value={:.5f}'.format(p_value),'z_score=%.3f'%z_score)
```

## t-test

小样本（样本量小于30个），一般用 t 检验。对于 t 检验，可以根据样本特点，用 scipy 包中的 ttest_1sample（单样本 t检验函数），ttest_ind（两个独立样本的 t 检验），ttest_rel （两个匹配样本的 t 检验）。但这些函数得到都是双侧 t 检验的 p 值。如果是单侧检验，我们还要进行一些换算，得到单侧检验的 p 值。

```
单样本t检验：
ttest_1sample(a,popmean)
a: 数组，单样本观测值
popmean:假设的总体均值

```
```py
from scipy import stats
sam1 = [99.3, 98.7, 100.5, 101.2, 98.3, 99.7, 99.5, 102.1, 100.5]
st.ttest_1samp(sam1, 100)

>>> Ttest_1sampResult(statistic=-0.054996133220328265, pvalue=0.9574902045208937)

```
双侧检验p-value为0.9575>a（0.05）接受原假设，样本sam1总体的均值为100。

如果要计算单侧检验：

1. 左侧：p-value / 2
2. 右侧：1 - p-value/2

```python
独立样本t检验
ttest_ind(a,b,equal_var=True)
a,b: 样本1，样本2
equal_var: bool,
		  如果两个总体方差相等，执行标准的t-test
		  如果两个总体方差不相等，执行welch's t-test
在做独立样本t检验时，需要检验两总体方差是否相等：之后会介绍几种方法。
```

```python
a = [99.3, 98.7, 100.5, 101.2, 98.3, 99.7, 99.5, 102.1, 100.5]
b = [91.1, 93.7, 93.6, 96.1, 94.3, 92.2, 94.0, 95.7, 97.1]
stats.levene(a,b)
#方差是齐的
stats.ttest_ind(a, b)
>>> Ttest_indResult(statistic=7.723221821038956, pvalue=2.4331092243754622e-06)

```

```
配对样本t检验
ttest_rel(a,b):Calculate the t-test on TWO RELATED samples of scores, a and b.
```

## 方差齐性检验

### Levene检验

这是最常用的一个检验方差齐性的检验。他不用考虑总体是不是服从正态分布。

levene是每个样本中的观测值减去各均值后的绝对值的检验，相当于误差项的检验，由[方差分析](https://pangzhengyang.github.io/2021/08/hypothesis-test/)可知：
$$
X_{ij} \sim N(\mu_j, \sigma^2)，因此X_{ij} - \mu_j \sim N(0, \sigma^2)，故X_{ij} - \mu_j可视为随机误差，记X_{ij} - \mu_j = \varepsilon_{ij} \\
\varepsilon \sim N(0,\sigma^2)
$$
误差项服从（0，方差），如果说各组方差相等，那么每个观测值应该来自一个总体，那么就不包括组间误差，F检验统计量=MSA/MSE就近似为1了

在python中直接调方法就可以了：

```py
from scipy import stats

stats.levene(a,b)
```

如果p-value>0.05,接受原假设：方差是齐的

综上可以得到：

```python
from scipy import stats


def ttest_twosides(a,b):
    '''
    双侧检验:ttest_ind()
    '''
    statistic,pvalue = stats.levene(a,b)
    if pvalue > 0.05:
        t_statistic,p_value = stats.ttest_ind(a,b,equal_var=True)
    else:
        t_statistic,p_value = stats.ttest_ind(a,b,equal_var=False)
    print ('=*-'*10+' Twoside-Test Result'+'=*-'*10)
    print ('T_statistic: ',t_statistic)
    print ('T_pvalue: ',p_value)

def ttest_oneside(a,b):
    '''
    单侧检验:ttest_ind()
    '''

    statistic,pvalue = stats.levene(a,b)
    if pvalue > 0.05:
        t_statistic,p_value = stats.ttest_ind(a,b,equal_var=True)
        if t_statistic>0:
            oneside_pvalue = 1 - p_value/2
        else:
            oneside_pvalue = p_value/2
    else:
        t_statistic,p_value = stats.ttest_ind(a,b,equal_var=False)
        if t_statistic>0:
            oneside_pvalue = 1 - p_value/2             
        else:
            oneside_pvalue = p_value/2
                
    print ('=*-'*10+'Oneside-Test Result'+'=*-'*10)
    print ('T_statistic: ',t_statistic)
    print ('T_pvalue: ',oneside_pvalue)
```

## 单因素方差分析（One-way ANOVA)

```py
import pandas as pd
data = pd.DataFrame([[1, 1, 32],
                     [1, 2, 35],
                     [1, 3, 35.5],
                     [1, 4, 38.5],
                     [2, 1, 33.5],
                     [2, 2, 36.5],
                     [2, 3, 38],
                     [2, 4, 39.5],
                     [3, 1, 36],
                     [3, 2, 37.5],
                     [3, 3, 39.5],
                     [3, 4, 43]], 
                    columns=['A', 'B', 'value'])
                    
  #只考虑一种因素A，他有3个水平分别为1,2,3
  stats.f_oneway(data[data['A']==1].value,
              data[data['A']==2].value,
              data[data['A']==3].value)
              
   #result：
   F_onewayResult(statistic=1.8602739726027395, pvalue=0.21077306261104234)
   
   #因为pvalue>0.05 接受H0，这三中水平的总体均值相等
```

## 正态分布检验

### KS-Test (Kolmogorov-Smirnov Test)

KS检验是一种非参检验方法，其通过比较两样本的频率分布、或者一个样本的频率分布与特定理论分布（如正态分布）之间的差异大小来推论两个分布是否来自同一分布。

```py
from scipy import stats
stats.kstest(rvs, cdf, args=(),…)
#其中rvs可以是数组、生成数组的函数或者scipy.stats里面理论分布的名字
#cdf可以与rvs一致。若rvs和cdf同是数组，则是比较两数组的分布是否一致；一个是数组，另一个是理论分布的名字，则是看样本是否否和理论分布
#args是一个元组，当rvs或者cds是理论分布时，这个参数用来存储理论分布的参数，如正态分布的mean和std。
```

```py
from scipy import stats

data = [87,77,92,68,80,78,84,77,81,80,80,77,92,86,
       76,80,81,75,77,72,81,72,84,86,80,68,77,87,
       76,77,78,92,75,80,78]
# 样本数据，35位健康男性在未进食之前的血糖浓度

df = pd.DataFrame(data, columns =['value'])
u = df['value'].mean()  # 计算均值
std = df['value'].std()  # 计算标准差
stats.kstest(df['value'], 'norm', (u, std))
# .kstest方法：KS检验，参数分别是：待检验的数据，检验方法（这里设置成norm正态分布），均值与标准差
# 结果返回两个值：statistic → D值，pvalue → P值
# p值大于0.05，为正态分布
```

### W检验(Shapiro–Wilk test)

```py
from scipy import stats
data = [0.86, 0.78, 0.83, 0.84, 0.77, 0.84, 0.81, 0.84, 0.81, 0.81, 0.80, 0.81,
       0.79, 0.74, 0.82, 0.78, 0.82, 0.78, 0.81, 0.80, 0.81, 0.74, 0.87, 0.78]
stat, p = stats.shapiro(data)
print("stat为：%f" %stat,"p值为：%f" %p)
#stat为：0.966175 p值为：0.574134
```



