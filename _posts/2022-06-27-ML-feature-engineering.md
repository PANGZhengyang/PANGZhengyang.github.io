---
layout: post
title: "特征工程"
date: 2022-06-27
description: "Feature Engineering"
tag: 机器学习
katex: true  
---

数据和特征决定机器学习的上限，而模型和算法只是逼近这个上限。特征工程的目的就是从原始数据中提取特征以供模型算法使用。

本文会总结特征处理需要注意的一些方法。

# 单个特征

- 异常值：箱图 - 去掉 或者 推测

- 重复值：删除

- 缺失值：删除 或者 填充（拉格朗日插值法、中值、平均数）

- 离散化：例如分箱bin，能提高线性回归建模能力使其更灵活

- 归一化：受异常值影响，其中range为max - min

  $$
  \frac{x - min}{range} 
  $$
  
  代码实现：
  
  ```python
  from sklearn.preprocessing import MinMaxScaler
  def range_normalization(X):
      range_norm = MinMaxScaler()
      data = range_norm.fit_transform(X)
      return data
  ```
  
- 标准化：受异常值影响小

  $$
  z-score = \frac{x - \mu}{\sigma}
  $$
  
  代码实现：
  
  ```python
  from sklearn.preprocessing import StandardScaler
  def z_score(X):
      std = StandardScaler()
      data = std.fit_transform(X)
      return data
  ```

- One-hot 编码

  将离散型特征的每一种取值都看成一种状态，若这一特征中有N个不相同的取值，那么我们就可以将该特征抽象成N种不同的状态，例如，occupation里有doctor和teacher，通过One-hot编码，可以生成新的变量：occ_doctor(0,1)， occ_teacher（0,1）

  代码实现：

  ```python
  pd.get_dummies(data)
  # 对于数字的分类可以先转换为string再使用get_dummies()
  ```

  **对于无序多分类变量，引入模型时需要转化为Dummy Variable**。举一个例子，如血型，一般分为A、B、O、AB四个类型，为无序多分类变量，通常情况下在录入数据的时候，为了使数据量化，我们常会将其赋值为1、2、3、4。从数字的角度来看，赋值为1、2、3、4后，它们是具有从小到大一定的顺序关系的，而实际上，四种血型之间并没有这种大小关系存在，它们之间应该是相互平等独立的关系。如果按照1、2、3、4赋值并带入到回归模型中是不合理的，此时我们就需要将其One-hot 编码，将这个血型特征转换为4个新的变量（血型-A，血型-B...)

- 数据变换

  大部分模型都在每个特征大致遵循正态分布时表现最好，换句话说，每个特征的直方图符合“钟型曲线”。所以可能会使用`log`,`exp`等函数进行变换

  # 多个特征

  ## 降维

  ### PCA

  主成分分析（PCA）是最常用的线性降维方法，它的目标是通过某种线性投影，将高维的数据映射到低维的空间中，并期望在所投影的维度上数据的信息量最大（方差最大），以此使用较少的数据维度，同时保留住较多的原数据点的特性。[PCA求解例子]([PCA（主成分分析）的理解与应用 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/60664507)) 和 [PCA算法过程]([(36条消息) PCA：详细解释主成分分析_lanyuelvyun的博客-CSDN博客_pca](https://blog.csdn.net/lanyuelvyun/article/details/82384179?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1-82384179-blog-111288390.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~default-1-82384179-blog-111288390.pc_relevant_default&utm_relevant_index=2))。代码实现如下：

  ```python
  from sklearn.decomposition import PCA
  
  pca = PCA(n_components=k) # k定义为几个主成分
  pca.fit(X.data)
  X_pca = pca.transform(X.data)
  
  ```

  优点：方法简单实现特征降维；

  缺点：主成分解释含义往往具有一定模糊性。

  

  ### LDA

  ## 特征选择

  ### 过滤式 Filter


  先对数据集进行特征选择，然后再训练学习器。思想：自变量与目标变量的关联。

  - 方差选择法

    对于连续型变量，看这个特征是否具有发散性，如果特征不发散（其方差接近0）说明样本在这个特征上基本没有差异，这个特征对于区分样本没有什么用。使用方差选择法，先要计算各个特征的方差，然后根据阈值，选择方差大于阈值的特征。使用`feature_selection`库的`VarianceThreshold`类来选择特征的代码如下：

    ```python
    from sklearn.feature_selection import VarianceThreshold
     
     #方差选择法，返回值为特征选择后的数据
     #参数threshold为方差的阈值
    VarianceThreshold(threshold=3).fit_transform(iris.data)
    ```

- 卡方检验
  
    更多对于离散型变量，检验其与目标变量的关系。用`feature_selection`库的`SelectKBest`类结合卡方检验来选择特征的代码如下：
    
    ```python
  from sklearn.feature_selection import SelectKBest
  from sklearn.feature_selection import chi2
#选择K个最好的特征，返回选择特征后的数据
    SelectKBest(chi2, k=2).fit_transform(iris.data, iris.target)
  ```

  - 相关性分析

    第一种是如果变量太多，也可以使用相关性分析得出两两变量的相关性，来选择特征。

    第二种是每个变量和Target的相关性，选择相关性高的特征。
  
    ```python
    from scipy.stats import pearsonr
    pearsonr(X,Y)
    ```
  
  - 方差分析
  
    对于分类问题。方差分析可以用于特征筛选，在建模的过程中，如果月消费金额这一字段对标签取值的预测能够起到帮助作用，一定是因为这个字段对标签具备一定的区分度，例如流失用户可能普遍月消费金额比较低，那么我们就能通过月消费金额较低的角度对用户是否可能会流失进行提前的预判；而反之，如果流失用户和非流失用户，这两批用户在月消费金额的角度完全看不出差异，那么月消费金额这一字段也不太可能对后续建模提供任何有效信息，该字段完全可以考虑删除。[方差分析用于特征选择例子]([(36条消息) 五、特征选择(filter)：方差分析(ANOVA)_ctf_ha的博客-CSDN博客_特征选择方差分析](https://blog.csdn.net/qq_34120015/article/details/124332050?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2-124332050-blog-105101146.pc_relevant_paycolumn_v3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2-124332050-blog-105101146.pc_relevant_paycolumn_v3&utm_relevant_index=5))

  ### 基于模型的特征选择 Embedded
  - 基于惩罚项的特征选择法
  
    使用带惩罚项的基模型，除了筛选出特征外，同时也进行了降维。使用feature_selection库的SelectFromModel类结合带L1惩罚项的逻辑回归模型，来选择特征的代码如下：
  
    ```python
    from sklearn.feature_selection import SelectFromModel
    from sklearn.linear_model import LogisticRegression
     
    #带L1惩罚项的逻辑回归作为基模型的特征选择
    SelectFromModel(LogisticRegression(penalty="l1", C=0.1)).fit_transform(iris.data, iris.target)
    
    ```
    
  - 基于树模型的特征选择法
  
    树模型中GBDT也可用来作为基模型进行特征选择，使用feature_selection库的SelectFromModel类结合GBDT模型，来选择特征的代码如下：
  
    ```python
    from sklearn.feature_selection import SelectFromModel
    from sklearn.ensemble import GradientBoostingClassifier
     
    #GBDT作为基模型的特征选择
    SelectFromModel(GradientBoostingClassifier()).fit_transform(iris.data, iris.target)
    
    ```
  ### 基于专家经验

  通常来说，领域专家可以找到非常有用的特征。
