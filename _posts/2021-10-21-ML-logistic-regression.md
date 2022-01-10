---
layout: post
title: "Logistic Regression"
date: 2021-10-21
description: "逻辑回归总结"
tag: 机器学习
katex: true
---

## Logistic Regression

Lg 本质上是一个广义的线性模型。做二分类任务，并能给出相应的概率。

线性方程

$$
Y = \theta_0 + \theta_1 x_1 +...+ \theta_n x_n = \theta^T x
$$

方程右侧是一个连续的值，取值为负无穷到正无穷，而左边我们希望是只取[0,1] 所以我们将使用`Sigmoid/ logistic function` 进行转换：

$$
y = \frac{1}{1+e^{-z}} \\ z = \theta^T x
$$

将`y` 视为 `y`取值为1的概率：

$$
P(Y=1 | x,\theta) = \frac{1}{1+e^{-\theta^T x}}
$$

`1-y` 即为 `y`取值为0的概率。

将上式调整一下, $\frac{y}{1-y}$ 为该事件发生与不发生的概率比值，记为`odds`

$$
\ln(odds)=\ln(\frac{y}{1-y}) = ln(\frac{P(Y=1 | x,\theta)}{1 - P(Y=1 | x,\theta)}) = \theta^T x
$$

我们可以发现 $\ln(odds)$ 是**任意阶可导的凸函数**，所以能够收敛，便于使用梯度下降等算法求出最优解。

## Cost function

$$
Cost(h_{\theta}(x),y) = 
\begin{cases} - \log(h_{\theta}(x)) &\text{if y =1}  
\\ -\log(1-h_{\theta}(x)) &\text{if y =0} \end{cases}
$$

如图：

![2021-11-21-ML-logistic-regression.png](/assets/machine learning/2021-11-21-ML-logistic-regression.png)

注：Cost function 是针对整个样本量，而loss function 是针对单个样本，区别在于**有没有把预测值和真实值的差异求和再取平均**

当真实值为1，Loss function为 $- \log(h_{\theta}(x))$，若我们判断为1时， Loss function最小；但判断为0时，Loss会非常大（作为惩罚）。

将上述式子合并得到cost function：

$$
J(\theta) = -\frac{1}{m} [\sum_{i=1}^{m} y^{(i)} \log({h_{\theta}(x^ {(i)} ) }) + (1-y^{(i)}) \log(1-{h_{\theta}(x^{(i)})})]
$$

Logistic regression的cost function 不是定义出来的，而是通过**极大似然估计推导出来的**

极大似然估计就是**通过已知的结果去反推最大概率可能得到这个结果的参数**。它提供了一种给定观察数据来评估模型参数的方法。Lg是一种监督式学习，是有训练标签的，就是有已知结果的，从这个已知结果入手，去推导能获得最大概率的结果参数，只要我们得出了这个参数，那我们的模型就自然可以很准确的预测未知的数据了。

令Lg模型为$h_{\theta}(x)$，将其视为类1的后验概率：

$$
P(Y=1 | x,\theta) = \frac{1}{1+e^{-\theta^T x}} \\
P(Y=0 | x,\theta) = \frac{e^{-\theta^T x}}{1+e^{-\theta^T x}}
$$

上面的式子可以改写为一般形式：
$$
P(Y|x,\theta) = h_{\theta}(x)^Y (1-h_{\theta}(x))^{1-Y}
$$

根据极大似然估计，可以得到：

$$
J(\theta) = \prod_i^m P(y^{(i)}|x^{(i)},\theta) = h_{\theta}(x^{(i)})^{Y^{(i)}} (1-h_{\theta}(x^{(i)}))^{{(1-Y)}^{(i)}}
$$

为了简化计算，取对数得到：

$$
\log(J(\theta)) = \sum_{i=1}^{m} y^{(i)} \log({h_{\theta}(x^{(i)})}) + (1-y^{(i)}) \log(1-{h_{\theta}(x^{(i)})})
$$

我们希望极大似然越大越好，就是说，对于给定样本数量m，希望$-\frac{1}{m} J(\theta)$ 最小，所以Lg的Cost function：

$$
J(\theta) = -\frac{1}{m} [\sum_{i=1}^{m} y^{(i)} \log({h_{\theta}(x^{(i)})}) + (1-y^{(i)}) \log(1-{h_{\theta}(x^{(i)})})]
$$

## Python实现

首先是`Sigmoid function` 使用`scipy`包中的函数：

```python
from scipy.special import expit
expit()
#给出了 y=1 的概率值
```

Logistic regression 可以直接从`sklearn`中调取：

```python
from sklearn.linear_model import LogisticRegression
lg_clf = LogisticRegression()
lg_clf.fit(X, y)
lg_clf.predict(X_test)
lg_clf.predict_proba(X_test)
```
常用属性：
(1) coef_ : 以数组形式返回假设函数中特征的系数
(2) intercept_ : 以数组形式返回假设函数的截距
常用方法：
(1) fit(X, y) : 对给定训练数据拟定模型
(2) predict(X) : 预测x中样本的类标签
(3) predict_proba(X) : 给出估计概率





