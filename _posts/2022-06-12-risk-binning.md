---
layout: post
title: "分箱"
date: 2022-06-12
description: "分箱"
tag: 风控
katex: true
---

分箱（Binning）就是将连续型变量离散化。

分箱的数量不宜太过稀疏，在评分卡中通常划分4-5个bin。

## 优点

1. 对异常数据有较强的鲁棒性
2. 增加了非线性，对于风控评分卡常用的逻辑回归（广义线性）增加了非线性的，可以提升模型表达能力

## 分类

1. **等频**

每箱的数据量都相同。适用于数据分布不均匀。

```python
import numpy as np
import pandas as pd

data = pd.DataFrame({'value': np.random.randint(1, 100, 100)})
n_bins = 5
data['bin'] = pd.qcut(data['value'],n_bins,labels=False)
data['bin_label'] = pd.qcut(data['value'],n_bins)
data
```



2. **等宽分箱**

等宽分箱将数据划分为固定大小的区间，通常指定区间的数量或宽度。适用于数值分布均匀的数据。

```python
import numpy as np
import pandas as pd

data = pd.DataFrame({'value': np.random.randint(1, 100, 100)})
n_bins = 5
data['bin'] = pd.cut(data['value'],n_bins,labels=False)
data['bin_label'] = pd.cut(data['value'],n_bins)
data
```

3. **聚类（K-means）**

使用聚类算法进行分箱。适合于数据分布不均。

```python
import numpy as np  
import pandas as pd  
from sklearn.cluster import KMeans  

# 示例数据  
data = {  
    'age': [25, 56, 45, 34, 23, 44, 56, 78, 23, 45],  
    'salary': [50000, 60000, 54000, 65000, 45000, 55000, 67000, 78000, 45000, 56000]  
}  

df = pd.DataFrame(data)  

# 定义要分成多少个簇  
k = 3  

# 使用K-means聚类  
kmeans = KMeans(n_clusters=k, random_state=0).fit(df)  

# 查看聚类结果  
df['cluster'] = kmeans.labels_  
print(df)  

# 输出每个簇的中心点  
print("Cluster Centers:", kmeans.cluster_centers_)

```

4. **CART决策树分箱**

该算法是**基于最小基尼指数递归的方式选择最优的二值划分点**，不断地把数据集划分成D1和D2两部分，依此类推直到满足停止条件。所以连续分箱也是可以借助相同的理论来使用，其实现步骤如下：
1）给定连续变量 V，对V中的值进行排序；

2）依次计算相邻元素间中位数作为二值划分点的基尼指数；

3）选择最优（**划分后基尼指数下降最大**）的划分点作为本次迭代的划分点；

4）递归迭代步骤2-3，直到满足停止条件。（一般是以划分后的样本量作为停止条件，比如叶子节点的样本量>=总样本量的10%）

[决策树分箱过程详解](https://mp.weixin.qq.com/s?__biz=Mzg4NDU4ODUxMA==&mid=2247500684&idx=1&sn=884c9b621d011ab53ca4c17020ec8cc3&chksm=cfb7693df8c0e02bff18d43ec4c790e4ecbd5d102e82f60ca0d2dd95d47cd6fc9be57b8eb100&scene=178&cur_album_id=2514778968466980866#rd)

1. **卡方分箱（ChiMerge）**

   该方法是基于卡方分布，计算卡方值（表示两个数据集的差异），**算出来的卡方值越小，就越证明这两个数据集是同一类，可以合并**。分成2阶段初始化阶段和自底向上合并阶段，实现步骤如下：

   1）给定连续变量 V，对V中的值进行排序，然后每个元素值单独一组，完成初始化阶段；

   2）对相邻的组，两两计算卡方值；

   3）合并卡方值最小的两组；

   4）递归迭代步骤2-3，直到满足停止条件。（一般是卡方值都高于设定的阈值，或者达到最大分组数等）

   ![卡方分箱的理论依据](\assets\risk\2022-06-12-risk-binning\1.png)

   卡方值越小，说明两个数据集具有相似的类分布：

   ![卡方越小，相似的类分布](\assets\risk\2022-06-12-risk-binning\2.png)

   

   