---
layout: post
title: "Clustering"
date: 2021-12-27
description: "Cluster"
tag: 机器学习
katex: true  
---

## Hierarchical  Clustering

层次聚类包括合并(Merge) 和分类(Split) 两种类型，以合并为例

**原理**：

1. 把数据集中每个样本点看成一个簇
2. 计算各个簇之间的距离，将距离最短的两个簇合并
3. 重复步骤2，直至剩余k个簇

```
计算各个簇之间的距离有多种方法：质心法(Centroid Method)，最小距离法(Single-linkage)，最大距离法(Complete-linkage)，平均距离法(Average-linkage)等。
```

注意：层次聚类中，k的选取可以参考一些指标：

1. $R^2$：值越大，说明分为 g 个类越好

$$
R^2 = \frac{SSB}{SST}
\\
SST = SSB +SSE
$$

2. $Semi-partial R^2$：值越大，合并造成的信息损失越大，说明两个类越不应该合并，

**优点**：不用决定k值

**缺点**：不适用于大样本

## K-means

**原理**：

1. 随机选取K个聚类中心
2. 计算各个样本点与聚类中心的距离，将样本点归入距离最近的聚类中心形成簇
3. 计算每个簇的均值中心作为新的中心
4. 重复步骤2和步骤3，直至聚类中心不再发生改变或达到指定迭代次数

**Cost Function**：

$$
J(c^{(1)},\dots ,c^{(m)},\mu_1 ,\dots ,\mu_K) = \frac{1}{m} \sum_{i=1}^m || x^{(i)} - \mu_{c^{(i)}}|| ^2
$$

$c^{(i)}$：样本$x^{(i)}$目前被分到簇中

$\mu_k$：聚类中心

$\mu_{c^{(i)}}$ ：样本$x^{(i)}$被分入的簇中的聚类中心

**如何选择K**：

- Elbow Method：`y`轴为Cost function，`x`轴为k的个数，寻找拐点
- 根据实际业务情况
- 可以先使用层次聚类得到聚类数K，在使用K-means方法

**优点**：

- 适用于大样本，速度快
- 聚类效果好
- 可解释性强

**缺点**：

- K值需要人为选取
- 随机选取初始点，可能造成局部最优

```
关于局部最优的问题，可以多次跑K-means算法，比如100次，计算其cost function，取其最小时的初始化点即可
```


