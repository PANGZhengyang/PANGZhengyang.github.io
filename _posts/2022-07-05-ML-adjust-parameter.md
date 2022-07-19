---
layout: post
title: "模型调参"
date: 2022-07-05
description: "Adjust Parameter"
tag: 机器学习
katex: true  
---

# 交叉验证

交叉验证（Cross-validation）是一种评估模型泛化性能的方法。

优点：对数据的使用更加高效；缺点：增加了计算成本。

## K折交叉验证（ K-fold cross-validation）

在这种 K 折交叉验证技术中，整个数据集被划分为 K 个相等大小的部分。 每个分区称为一个“折叠”。因此，因为我们有 K 个部分，所以我们称之为 K 折叠。 一折用作验证集，其余 K-1 折用作训练集。该技术重复 K 次，直到每个折叠用作**验证集**，其余折叠用作训练集。

模型的最终精度是通过取 k-models **验证数据**的平均精度来计算的。我们最后的评估结果使用test dataset，在此之前不要将模型运用到test数据集上，造成“污染”。

![K-fold cross-validation](\assets\machine learning\2022-07-05-ML-adjust-parameter\k-fold-cross-validation.png)

图上的划分方式更适合风控领域，但是有些时候也不单独把test拿出来，（如果要拿出来，则在最开始使用train_test_split）而是揉在一起，将整个data分成k-fold，然后计算泛化结果，搭配网格搜索，调参。

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import cross_val_score,KFold
from sklearn.linear_model import LogisticRegression
iris=load_iris()
X=iris.data
Y=iris.target
logreg=LogisticRegression()
kf=KFold(n_splits=5) # 交叉验证分离器
score=cross_val_score(logreg,X,Y,cv=kf)
print("Cross Validation Scores are {}".format(score))
print("Average Cross Validation score :{}".format(score.mean()))
```

优点： 整个数据集既用作训练集又用作验证集

缺点：不适合不平衡的数据集

## 分层K折交叉验证（stratified k-fold cross-validation）

分层 K-Fold 是 K-Fold 交叉验证的增强版本，**主要用于不平衡的数据集。** 就像 K-fold 一样，整个数据集被分成大小相等的 K-fold。但是在这种技术中，每个折叠将具有与整个数据集中相同的目标变量实例比率。

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import cross_val_score,StratifiedKFold
from sklearn.linear_model import LogisticRegression
iris=load_iris()
X=iris.data
Y=iris.target
logreg=LogisticRegression()
stratifiedkf=StratifiedKFold(n_splits=5)
score=cross_val_score(logreg,X,Y,cv=stratifiedkf)
print("Cross Validation Scores are {}".format(score))
print("Average Cross Validation score :{}".format(score.mean()))
```

## 留一法（Leave-one-out）

只从可用的数据集中保留一个数据点，并根据其余数据训练模型。此过程对每个数据点进行迭代，比如有n个数据点，就要重复交叉验证n次。例如下图，一共10个数据，就交叉验证10次。

![leave-one-out cross-validation](\assets\machine learning\2022-07-05-ML-adjust-parameter\leave-one-out.png)

优点：适用于小样本数据集，利用所有数据点；缺点：计算量过大

```python
from sklearn.datasets import load_iris
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import LeaveOneOut,cross_val_score
iris=load_iris()
X=iris.data
Y=iris.target
loo=LeaveOneOut()
tree=RandomForestClassifier(n_estimators=10,max_depth=5,n_jobs=-1)
score=cross_val_score(tree,X,Y,cv=loo)
print("Cross Validation Scores are {}".format(score))
print("Average Cross Validation score :{}".format(score.mean()))
```

# 网格搜索

通常情况下，**有很多参数是需要手动指定的（如k-近邻算法中的K值），这种叫超参数**。但是手动过程繁杂，所以需要对模型预设几种超参数组合。**每组超参数都采用交叉验证来进行评估。最后选出最优参数组合建立模型。**

```
GridSearchCV(estimator, param_grid, cv)
- 该类对估计器指定的参数值进行详尽搜索
- estimator： 估计器对象
- param_grid：估计器参数 是个dict 例如：{'n_neighbors:[3,5,7,9]'}
- cv：指定几折交叉验证

实例化后的GridSearchCV，可以使用：
- fit：会对param_grid指定的每种参数组合都运行交叉验证，拟合后的GridSearchCV对象不仅会搜索最佳参数，还会利用最佳交叉验证性能的参数在整个训练数据集上自动拟合一个新模型。
- score：使用最佳模型得出：准确率
- predict：分类预测

结果分析：
- best_params_：最佳参数
- best_score_：交叉验证最佳精度
- cv_result_：每次交叉验证后的验证集准确率和训练集准确率结果
```

示例代码：

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.model_selection import GridSearchCV
from sklearn.svm import SVC

iris = load_iris()

# 设置超参数网格
param_grid = {'C':[0.001, 0.01, 0.1, 1, 10, 100],
             'gamma':[0.001, 0.01, 0.1, 1, 10, 100]}
grid_search = GridSearchCV(SVC(), param_grid, cv=5)
X_train, X_test, y_train, y_test = train_test_split(iris.data, iris.target, random_state=0)
grid_search.fit(X_train, y_train)

# 模型最优参数
grid_search.best_params_
# 交叉验证最佳精度
grid_search.best_score_

# 评估最佳参数的泛化能力
grid_search.score(X_test,y_test)
```



