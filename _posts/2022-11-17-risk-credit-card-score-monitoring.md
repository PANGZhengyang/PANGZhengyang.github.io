---
layout: post
title: "信用评分卡监控"
date: 2022-11-17
description: "逻辑回归评分卡"
tag: 风控
katex: true
---

评分卡模型监控主要分为前端监控和后端监控。前端监控针对模型稳定性，对于评分稳定性使用PSI，对于特征稳定性使用CSI。后端监控针对模型正确性。对于模型母体鉴别度使用KS、Gini系数，对于特征鉴别度使用IV。

# 前端监控

前端监控关注样本人群稳定性。

## PSI

PSI（Population Stability Index）群体稳定性指标，用于衡量现行样本和开发样本的分布差异，反映分数分布的稳定性。

计算公式：
$$
PSI = \sum_i^n (A_i - E_i) \times ln(\frac{A_i}{E_i})
$$
其中$A_i$表示现行样本占比，$E_i$表示开发样本占比，n表示第n箱

计算步骤：

1. 将用于开发模型的样本作为Expected，并按照评分进行分箱（一般按照等频即每箱样本数相等）进行划分，求得每箱样本占比（$E_i$=该箱样本数/总样本数）
2. 对现行样本Actual按照同样的方法进行分箱，求得$A_i$
3. 根据PSI计算公式算出每箱的Index
4. 累加Index得到PSI

计算实例：

![PSI计算](\assets\risk\2022-11-17-risk-credit-card-score-monitoring\1.png)

评判标准：

|      PSI      | 模型稳定性 | 分布情况 |    Action    |
| :-----------: | :--------: | :------: | :----------: |
|   [0, 0.1)    |     好     |   轻微   |    不需要    |
|  [0.1, 0.25)  |    一般    |   较小   | 监控后续变化 |
| [0.25,正无穷) |   不稳定   |   较大   | 进行特诊分析 |

若PSI大于0.25，表明母体分布变化较大，但并不代表评分模型所有变量都呈现不稳定，可以通过特征稳定性指标CSI来了解什么造成母体偏移。

## CSI

CSI（Characteristic Stability Index）衡量样本在特征层面上的变化，反映特征对评分卡分数变化的影响。

计算公式：
$$
CSI = \sum_i^n(A_i-E_i)Score_i
$$
其中，n表示某变量的第n个箱，$A_i - E_i$表示某特征箱子内现行样本与开发样本占比差距，$Score_i$表示第i个箱子的评分，由该变量每箱的Woe和该变量的拟合系数共同决定,[变量的分数可以看这一篇](https://pangzhengyang.github.io/2022/05/risk-credit_card_score_mapping/)

计算实例：

![CSI计算](\assets\risk\2022-11-17-risk-credit-card-score-monitoring\2.png)

评判标准：

无标准参考。CSI越大代表变量越不稳定，当特征的CSI为正，特征分布变化使模型得分朝高分偏移，反之，当特征CSI为负，特征分布变化使模型得分朝低分偏移。

# 后端监控

后端监控关注模型的鉴别能力（正确性）。母体鉴别能力：KS、Gini；特征鉴别能力：IV。

## KS

KS（Kolmogorov-Smirnov）衡量母体鉴别能力。各评分组别中，好坏客户累计占比差距最大的数值。

计算公式：
$$
KS = \max |TPR - FPR|
$$
其中TPR就表示好客户累计占比，FPR表示坏客户累计占比。

图例：

![KS](\assets\risk\2022-11-17-risk-credit-card-score-monitoring\3.png)

判断标准：

KS越大表示鉴别能力越好，KS大于0.2表示具有良好的鉴别能力。

## Gini

Gini曲线衡量母体鉴别能力。在各评分组别中，以坏客户累计占比为横轴，以好客户累计占比为纵轴，做散点图，再将其连线。

图例：

![Gini曲线](\assets\risk\2022-11-17-risk-credit-card-score-monitoring\4.png)

AC为无判别能力，每个分组好坏客户累计占比相同；ABC为完全鉴别能力，某一评分组别以下均为坏客户，高于此评分组别的均为好客户。AGC为洛伦兹曲线（Gini曲线），越接近ABC模型鉴别能力越好。

计算公式：
$$
Gini = \frac{S_{AGCA}}{S_{ABC}} = \frac{S_{ABC} - S_{ABCGA}}{S_{ABC}} = \frac{0.5 - S_{ABCGA}}{0.5} 
$$
其中ABCGA的面积可以用各评分组别的斜线面积来结算，斜线面积近似于梯形面积公式：上底为$e_{i-1}$，下底为$e_i$，高度为$f_i - f_{i-1}$，所以有：
$$
S_{ABCGA} = \sum_{i=1}^{评分组别} \frac{(e_{i-1}+e_i) \times (f_i - f_{i-1})}{2}
$$
判断标准：

Gini系数大于0.4表示模型鉴别能力较好。

## IV

IV（Information Value）衡量变量的鉴别能力。

计算公式：
$$
变量IV = \sum_{i=1}^{n} (占比差距_i) \times (占比权重_i)
$$
其中，n表示变量中的每个bin，$占比差距_i$表示第i个bin中，坏客户数量占总坏客户数量（Bad Distribution) 与 Good Distribution之差；占比权重相当于Woe，表示第i个bin中，ln(Bad Distribution / Good Distribution)。

判断标准：

IV大于0.02表示该变量有鉴别能力。

