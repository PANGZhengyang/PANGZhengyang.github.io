---
layout: post
title: "Linear Regression"
date: 2021-10-15
description: "线性回归总结"
tag: 机器学习
katex: true
---

# Linear regression

## Cost Function

线性回归的代价函数：

$$
J(\theta) = \frac{1}{2m} \sum_{i=1}^{m} (h_{\theta}(x)-y)^2
$$

其中：

$$
h_{\theta}(x) = \theta ^T x = \theta_0 +{\theta_1}x_1 +\cdots+{\theta_n}x_n
$$

下面就是要求出$\theta$，使代价函数最小：即拟合出的方程到真实值距离的平方最小；前面有系数2是因为后面使用梯度下降的时候，对每个变量求偏导，可以消去。

代码实现：

```python
def computerCost(X, y, theta):
    m = len(y)
    J = 0
    
    J = (np.transpose(X*theta-y))*(X*theta-y) / (2*m)
    return J
```

## 梯度下降

代价函数对$\theta_j$求偏导：

$$
\frac{\partial J(\theta)}{\partial \theta_j} = \frac{1}{m} \sum_{i=1}^{m}[(h_{\theta}(x^{(i)})-y^{(i)})x_j^{(i)}]
$$

对$\theta$同时更新：

$$
\theta_j = \theta_j - \alpha \frac{1}{m} \sum_{i=1}^{m}[(h_{\theta}(x^{(i)})-y^{(i)})x_j^{(i)}]
$$

其中，$\alpha$ 为学习效率`learning rate`，大小可以控制下降的速度（步子迈的大小），注意

- $\alpha$过大，可能会造成代价函数不能收敛
- $\alpha$过小，收敛的速度会很慢,所需迭代次数会非常多

根据经验，通常$\alpha$ 会选择`0.01`,`0.03`,`0.1`,`0.3`,`1`,`3` ...

## 特征缩放

由于不同特征有不同量纲，值的大小相差过大会导致梯度下降速度变慢且默某些参数也会不灵敏，所以特征缩放很有必要。

- 归一化 `Range Normalization` 把数据映射为[0,1]之间
  
  $$
  x' = \frac{x - \min(x)}{range} \\
  range = \max - \min
  $$
  
  作用于每一列`max`为一列的最大值。代码实现：

  ```python
  from sklearn.preprocessing import MinMaxScaler
  def range_normalization(X):
      range_norm = MinMaxScaler()
      data = range_norm.fit_transform(X)
      return data
  ```
  
- 标准化`Standardization` 把数据变换到均值为0，方差为1的范围内
  
  $$
  z-score = \frac{x - mean}{\sigma}
  $$
  代码实现：

  ```python
  from sklearn.preprocessing import StandardScaler
  def z_score(X):
      std = StandardScaler()
      data = std.fit_transform(X)
      return data
  ```

  ## 正规方程

  对于线性回归求解参数还可以使用正规方程。
  
  $$
  \theta = (X^TX)^{-1}X^Ty
  $$
  
  正规方程与梯度下降的比较：

  | 梯度下降                        | 正规方程                      |
  | ------------------------------- | ----------------------------- |
  | 需要选择`learning rate`$\alpha$ | 不需要                        |
  | 需要多次迭代                    | 一次                          |
  | 当特征数量n比较大时也适用       | 特征数量n太大计算量就非常大了 |
  | 适合各种类型的模型              | 只适用于线性模型              |

  代码实现：

  ```python
  import numpy as np
  
  def normalEquation(X, y):
    # X.T@X等价于X.T.dot(X)  @等价于.dot
    theta = np.linalg.inv(X.T@X)@X.T@Y  
    return theta
  ```
  
  正规方程的推导过程：
  
  $$
  J(\theta) = \frac{1}{2m} \sum_{i=1}^{m} (h_{\theta}(x)-y)^2 \\
  J(\theta) = \frac{1}{2} (X \theta - y)^T (X \theta-y)= \frac{1}{2} (\theta^T X^T - y^T)(X\theta-y) \\
  =\frac{1}{2}(\theta^T X^T X \theta - \theta^T X^T y - y^T X \theta + y^T y)
  $$
  
  [矩阵求导公式]()：
  
  $$
  \frac{dA^T X}{dX} = A \\
  \frac{dX^T A X}{dX} = AX+A^T X
  $$
  
  所以有：
  
  $$
  \frac{\partial J(\theta)}{\partial \theta_j} = \frac{1}{2m}(2X^T X\theta -X^T y- X^T y+0) = \frac{1}{m}(X^T X\theta - X^T y)
  $$
  
  令：
  
  $$
  \frac{\partial J(\theta)}{\partial \theta_j} = 0 \\
  \theta = (X^T X)^{-1} X^T y
  $$
  

