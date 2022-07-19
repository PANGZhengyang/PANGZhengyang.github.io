---
layout: post
title: "Python Base Study——Sklearn"
date: 2020-06-12
description: "Python学习笔记"
tag: Python
---

This is my note for Python for Sklearn. When I face some tricky problems, I'll record them.
Let's start!

## 训练数据和测试数据

```python
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, t_test = train_test_split(X, y, random_state, test_size)
# random_state: 随机数种子，不同的种子会造成不同的随机采样结果。相同的种子采样结果相同
# test_size: test集的大小，20%、25%、30%
```

## 转换器

我们把特征工程的接口称之为转换器，其中转换器调用有这么几种形式：

- fit：简单来说，就是求得训练集X的均值，方差，最大值，最小值,这些训练集X固有的属性。
- transform：在fit的基础上，进行标准化，降维，归一化等操作（看具体用的是哪个工具，如PCA，StandardScaler等）
- fit_transform：fit和transform的组合，既包括了训练又包含了转换。

**对剩余的数据（testData）使用同样的均值、方差、最大最小值等指标进行转换transform(testData)**，**从而保证train、test处理方式相同**。

```python
from sklearn.preprocessing import StandardScaler
sc = StandardScaler()
sc.fit_transform(X_train)
sc.transform(X_test)
```

## 估计器

估计器(estimator)是一个重要的角色，是一类实现了算法的API

```python
# 监督
# 分类算法
from sklearn.neighbors import KNeighborsClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
from sklearn.ensemble import GradientBoostingClassifier

# 回归模型
from sklearn.linear_model import LinearRegression
from sklearn.linear_model import Lasso # L1 正则
from sklearn.linear_model import Ridge # L2 正则

# 无监督
# 聚类
from sklearn.cluster import KMeans

# 降维
from sklearn.decomposition impmort PCA

```

我们需要将算法的类实例化对象，才能使用这个模型。以KNN算法为例：

```python
from sklearn.neighbors import KNeighborsClassifier
knn = KNeighborsClassifier(n_neighbors=1)
knn.fit(X_train, y_train) # 调用knn对象的fit方法
knn.predict(X_test) # 预测结果
knn.score(X_test, y_test) # 结果精度
```







