---
layout: post
title: "ScoreCardpy评分卡建模"
date: 2022-08-23
description: "ScoreCardpy评分卡建模"
tag: 风控
katex: true
---

本文会介绍一下ScoreCardpy评分卡建模的使用方法。

## 明确目标

做评分卡一定要先把Target确定好

## 特征工程

寻找和Target相关的特征

## scorecardpy评分卡

```python
import pandas as pd
import numpy as np
import scorecardpy as sc
import pandas_profiling

dat = sc.germancredit()

# 前面这几步只是对于germancredit这个数据集做一些预处理
dat = dat.replace('none', np.nan)
for c in dat.columns:
    if str(dat[c].dtype) == "category":
        dat[c] = dat[c].astype(str)
    print(c,"：",len(dat[c].unique()))
    
# EDA
pandas_profiling.ProfileReport(dat)

# 可以发现other.debtors.or.guarantors，other.installment.plans，缺失值较高，删除
# 可以我们手动删除
dat = dat.drop(columns=["other.debtors.or.guarantors","other.installment.plans","telephone"])

# var_filter()
dt_s = sc.var_filter(dat, y='creditability', iv_limit=0.02, missing_limit=0.95)

# spilt_df()
train,test = sc.split_df(dt=dt_s,y="creditability").values() #方法：随机抽样

train.creditability.value_counts() #训练数据y的统计
test.creditability.value_counts() #测试数据y的统计

# 分箱并计算iv
bins = sc.woebin(dt_s, y='creditability', method='chimerge')
iv_report = pd.concat(bins).reset_index().drop(columns='level_0')
iv_report.rename(columns={'level_1':'bin_num'},inplace=True)
iv_report

# 画iv图和badrate图
sc.woebin_plot(bins["age.in.years"])
sc.woebin_plot(bins) # 画出全部变量的badrate图

# 分箱调整
break_adj = {
    'age.in.years':[26,35,45],
    'credit.amount':[750,3000,5500]
}
bins_adj = sc.woebin(dt_s,y='creditability',breaks_list=break_adj)
bins_adj_df = pd.concat(bins_adj).reset_index().drop(columns="level_0")
bins_adj_df

# Woe转换
train_woe = sc.woebin_ply(train, bins_adj)
test_woe = sc.woebin_ply(test, bins_adj)

# 建立模型
y_train = train_woe.loc[:,'creditability']
X_train = train_woe.loc[:,train_woe.columns != 'creditability']
y_test = test_woe.loc[:,'creditability']
X_test = test_woe.loc[:,train_woe.columns != 'creditability']

from sklearn.linear_model import LogisticRegression
lr = LogisticRegression(penalty='l1', C=0.9, solver='saga', n_jobs=-1)
lr.fit(X_train, y_train)

train_pred = lr.predict_proba(X_train)[:,1]
test_pred = lr.predict_proba(X_test)[:,1]

# 模型评价
train_perf = sc.perf_eva(y_train, train_pred, title = "train")
test_perf = sc.perf_eva(y_test, test_pred, title = "test")

# 评分卡转换
card = sc.scorecard(bins_adj, lr, X_train.columns)
pd.concat(card)

# 评分稳定性评估
train_score = sc.scorecard_ply(train, card, print_step=0)
test_score = sc.scorecard_ply(test, card, print_step=0)

sc.perf_psi(
    score = {'train':train_score,'test':test_score},
    label = {'train':y_train,'test':y_test}
)
```



### var_filter()

这个函数可以根据制定的条件筛选变量，例如IV值、缺失率等

```python
def var_filter(dt, y, iv_limit=0.02, missing_limit=0.95, identical_limit=0.95, var_rm=None, var_kp=None, return_rm_reason=False, positive='bad|1')
```

- identical_limit：一致性
- var_rm：强制删除变量的名称
- var_kp：强制保留变量的名称
- return_rm_reason：是否返回每个变量被删除的原因
- positive：坏样本的标签

如果变量太多的话，可以先用这个函数去做一遍初筛，后续在进行分箱计算IV筛选变量。



### split_df()

这个函数用来切分样本，该函数的ratio默认为0.7，即按照7:3对数据集进行分割。

```python
def split_df(dt, y, ratio=0.7, seed=186)
```

通常切分为train，test集写法：

```python
train, test = sc.split_df(dt_s, y='creditability').values()
```



### woebin()

这个函数用于将变量进行分箱，支持决策树分箱、卡方分箱和自定义分箱。默认的woe值是使用坏样本比率/ 好样本比率，这个可以通过参数positive进行调整。

```python
def woebin(dt, y, x=None, 
           var_skip=None, breaks_list=None, method="tree", bin_num_limit=8
           count_distr_limit=0.05, replace_blank=True, ,special_values=None, stop_limit=0.1,
           # min_perc_fine_bin=0.02, min_perc_coarse_bin=0.05, max_num_bin=8, 
           check_cate_num=True, positive="bad|1", no_cores=None, print_step=0,
           ignore_const_cols=True, ignore_datetime_cols=True, 
           save_breaks_list=None, **kwargs):
```

重要参数解释：

- var_skip：指定不需要分箱的变量
- breaks_list：分割点的list。对分箱进行调整的时候使用，在做分箱调整或自定义分箱的时候传入字典
- method：分箱方法，'tree'和'chimerge'
- bin_num_limit：最大分箱数
- count_distr_limit：分箱结果中最小占比，默认0.05
- replace_blank：将空值替换成None
- special_values：指定单独的箱
- stop_limit：当IV值的增加值小于stop_limit或者卡方值小于qchisq(1-stop_limit, 1)时停止分割
- check_cate_num：检查分类变量中类别数是否大于50

返回是每个变量的分箱结果组成的字典。然后计算每个分箱的woe和变量的iv报告 通常会结合下面这段语句：

```python
pd.concat(bins).reset_index().drop(columns='level_0')
```

![分箱以及woe和iv计算](\assets\risk\2022-08-23-risk-scorecardpy\1.png)

### woebin_plot()

该函数可以画出变量分箱之后每一箱的好坏样本数、样本占比、坏样本率。

坏样本率最好应该呈现单调性，其实坏样本率和woe的趋势是一致的
$$
WoE = \ln (Bad \ rate / Good \ rate)
$$
Bad rate 越大 Woe越大，所以趋势是一样的。

```python
sc.woebin_plot(bins)
```

根据画出来的badrate图，判断其单调性，进行手动调整分箱。

![Badrate图](\assets\risk\2022-08-23-risk-scorecardpy\2.png)

### 手动调整分箱

其实就是使用`sc.woebin()`函数，其中传入参数`breaks_list`

```python
break_adj = {
    'age.in.years':[26,35,45],
    'credit.amount':[750,3000,5500]
}

bins_adj = sc.woebin(dt_s, y='creatability', breaks_list=break_adj)
bins_adj_df = pd.concat(bins_adj).reset_index().drop(columns='level_0')

```

![手动调整后Badrate图](\assets\risk\2022-08-23-risk-scorecardpy\3.png)

### woebin_ply()

该函数可以将数据进行Woe编码

```python
train_woe = sc.woebin_ply(train, bins_adj)
test_woe = sc.woebin_ply(test, bins_adj)
```

### 建立模型

将WoE编码得到的变量喂到模型中，一般是逻辑回归。

```python
from sklearn.linear_model import LogisticRegression

lr = LogisticRegression(penalty='l1',C=0.9,solver='saga',n_jobs=-1)  
lr.fit(X_train,y_train)

train_pred = lr.predict_proba(X_train)[:,1]
test_pred = lr.predict_proba(X_test)[:,1]
```

`predict_proba()`方法可以得到二分类问题中 0和1 取值的概率 (P(0), P(1))...

### perf_eva()

该函数可以从KS、ROC、AUC、Lift曲线、PR曲线评估模型效果。

```python
def perf_eva(label, pred, title=None, plot_type=['ks','roc'], show_plot=True, positive='bad|1', seed=186)
```

其中重要参数：

- label：y_train 或者 y_test
- pred：train_pred 或者 test_pred
- plot_type：'ks' , 'roc' , 'lift' , 'pr'

![KS、ROC](\assets\risk\2022-08-23-risk-scorecardpy\4.png)

### scorecard()

模型评估后，可能需要对概率进行映射，转化成评分卡得分。得分包括每个客户最终得分和单个变量得分。

```python
def scorecard(bins, model, xcolumns, points0=600, odds0=1/19, pdo=50, basepoints_eq0=False)
```

其中重要参数：

- bins：分箱信息。woebin()返回的结果。
- model：模型对象。
- xcolumns：X_train.columns
- points0：基础分，默认600
- odds0：好坏比，默认1:19
- pdo：比率翻番的倍数，默认50
- basepoints_eq0：如果为True，则将基础分分散到每个变量中

### scorecard_ply()

该函数可以将数据进行评分卡打分

```python
train_score = sc.scorecard_ply(train, card, print_step=0)
test_score = sc.scorecard_ply(test, card, print_step=0)
```

### perf_psi()

该函数可以计算出PSI，并画出相关图。模型在训练数据得到的实际分布(A)，与测试集上得到的预期分布(E)，PSI越小，说明模型越稳定。通常PSI小于0.1，模型稳定性好。

```python
sc.perf_psi(
    score = {'train':train_score,'test':test_score},
    label = {'train':y_train,'test':y_test}
)
```

![PSI](\assets\risk\2022-08-23-risk-scorecardpy\5.png)

