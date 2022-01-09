---
layout: post
title: "马卡龙App项目"
date: 2021-09-15
description: "ABTest-马卡龙app项目"
tag: 数据分析
katex: true
---

## 项目概述

马卡龙玩图是一款视觉创作App，主要功能是AI抠图，换背景等。本文是对马卡龙玩图其中一个AB Test项目的复盘与总结，记录项目的具体分析与实现过程。所呈现的数据已经脱敏。

## 项目背景

马卡龙玩图App的盈利模式主要包括两种：

1. 广告盈利：包括开屏、Banner广告等；
2. PRO会员：让用户付费订阅会员，享受更多功能，包括去水印、无限草稿、发丝级抠图、艺术家模板、滤镜、背景等。

从长期盈利效果和用户体验来看，采用用户付费订阅会员的模式会更胜一筹，为此，计划在双十一前后，针对App里的免费用户做一次促销，吸引他们付费。

该项目主要是测试两条广告语哪一条更有效。

## 项目目标与假设

**确定目标**：使更多的免费用户升级成为付费用户。

通过运营、产品等部门沟通后确定两条广告语：

- **对照组广告语**：更多专业功能、千万艺术模板无限使用，用户升级，免费试用一个月！
- **实验组广告语**：即日起到11月15日，用户升级，免费试用一个月！

测试原型图：

![2021-09-15-versa-1](/assets/2021-09-15-versa-1.png)

**提出假设：**通过在广告语中加入倒计时这种增加紧迫感的信息，能够提升免费用户的升级率。

## 确定评价指标和护栏指标

**评价指标**：用户升级率 = 点击广告升级用户数 / 看到广告用户数

**评价指标波动范围：**[1.82%，2.18%]

通过对历史数据的回溯性分析，得到了用户在符合触发条件后升级为平均概率为 2.0%，通过统计公式最后求得该指标 95% 的置信区间为[1.82%，2.18%]。
$$
p \pm z_\frac{0.95}{2} \sqrt{\frac{2 \% * (1-2 \%)}{23240}} = [1.82\%,2.18\%]
$$
这就说明如果测试结束后两组评价指标的值均落入这个波动范围内，则说明两组无显著不同，属于正常波动范围。

**护栏指标：**对照组和测试组样本量比例、对照组和测试组性别比例

## 确定实验单位

因为本次实验的弹窗是用户可见的变化，而且评价指标是以用户为单位，所以选择用户作为最小实验对象单位，具体来说,**选择免费用户ID**

## 随机分组

实验组和对照组的流量各为50%

## 计算样本量大小和实验所需时间

- 显著水平 a = 5%
- Power = 80%
- 实验组和对照组评价指标的差值$\delta$ = 0.2%
- 样本标准差 $\sigma^2$ = $ p_{control}(1-p_{control})+p_{test}(1-p_{test})$ = 4.1%

说明：根据之前算出的波动性，两者的差值要在0.18%以上，才是统计显著的变化，所以我们直接取0.2%。

利用样本量计算器可以很容易计算出：我们算出实验组和对照组各需要至少 8.07 万个符合条件的用户，一共需要 16.14 万用户。而数据分析显示每天免费用户的流量大约为 1.7 万人，所以本次实验大约需要 10 天时间完成。

![2021-09-15-versa-2](/assets/2021-09-15-versa-2.png)

## 实施测试

在完成整个AB Test的设计工作之后，让测试跑起来，收集数据，等到样本量达到预期分析结果。

## 分析测试结果

我们实验组的样本量 80723，对照组的样本量为 80689

### 合理性检验

- **实验/对照组的样本大小的比例是否为50% / 50%**

  $$
  \sqrt{\frac{p(1-p)}{n}} = \sqrt{\frac{0.5(1-0.5)}{(80723+80689)}} = 0.12\%
  $$

  构建95%的置信区间[50%-1.96 * 0.12%,50+1.96 * 0.12%] = [49.76%,50.24%]
  也就是两组占比的波动范围。

  总体的实验组 / 对照组的样本量比例 =50.01%/49.99%。

  可以看到，两组占比均在置信区间内，属于正常波动。也就是说，两组样本量符合均分的预期，成功通过了实验 / 对照组样本量的比例这个合理性检验。

- 实验 / 对照组中特征的分布相似，也是通过合理性检验

### 统计显著性分析

|  组别  | 样本量 | 升级用户数 | 升级率 |
| :----: | :----: | :--------: | :----: |
| 对照组 | 80689  |    1598    | 1.98%  |
| 实验组 | 80723  |    3124    | 3.87%  |

使用python或者一些[在线工具](https://abtestguide.com/calc/) :

```python
from statsmodels.stats.proportion import proportions_ztest 

z_score, p_value = proportions_ztest([3124, 1598], [80723, 80689], alternative='two-sided')
print('p_value={:.5f}'.format(p_value),'z_score=%.3f'%z_score)
```

```
p_value=0.00000 z_score=22.525
```

![2021-09-15-versa-3](/assets/2021-09-15-versa-3.png)	

根据 P 值远远小于0.05，拒绝$H_0$,说明两组指标具有显著的不同，这就意味着实验组的广告语确实能提升免费用户的升级率。
