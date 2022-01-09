---
layout: post
title: "范数、过拟合与正则化"
date: 2020-07-21
description: "机器学习的一些基本概念笔记"
tag: 机器学习
katex: true
---

## 范数

范数，是**距离**概念的扩充。为了更好的实现比较。

比如，在一维实数集合中，我们随便取两个点4和9，我们知道9比4大，但是到了二维实数空间中，取两个点（1，1）和（0，3），这个时候我们就没办法比较它们之间的大小，因为它们不是可以比较的实数，于是我们引入范数这个概念(这里使用第二范数)，把我们的（1，1）和（0，3）通过范数分别映射到实数$\sqrt{2}$和 3 ，这样我们就比较这两个点了。所以范数它其实是一个函数，它把不能比较的向量转换成可以比较的实数。

## L-P范数

向量范数的通用公式为L-P范数
$$
Lp=\sqrt[p]{\sum_1^n x_i^p}，x=(x_1,x_2, \cdots,x_n)
$$
演化成各类范数：

- L0范数：非0个数
  $$
  ||x||=\sqrt[0]{\sum_1^n x_i^0}，x=(x_1,x_2,\cdots,x_n)
  $$
  
- L1范数：x与0之间的曼哈顿距离，每个元素绝对值之和
  $$
  ||x||_1=\sum_i|x_i|，x=(x_1,x_2,\cdots,x_n)
  $$
  
- L2范数：x与0质检的欧式距离，每个元素平方之和再开方
   $$
   ||x||_2=\sqrt {\sum x_i^2}
   $$
   
- L$\infty$ 范数:度量向量元素的最大值
   $$
   ||x||_\infty=\sqrt[\infty]{\sum_1^n x_i^\infty}，x=(x_1,x_2,\cdots,x_n) \\
   ||x||_\infty=max(|x_i|)
   $$

## 过拟合与欠拟合

由于模型复杂，参数过多，造成在训练阶段模型表现非常好，但是其泛化能力较弱。

这里要提到两个概念：偏差bias和方差variance。如图：

![2020-07-21-ML-overfit-regularization-1](/assets/machine learning/2020-07-21-ML-overfit-regularization-1.png)

- bias描述的是针对**训练集**，根据(样本拟合出的模型的输出)**预测结果和真实结果**的距离; low bias --- 模型复杂，参数过多，泛化能力差---造成过拟合 high variance --- 只要数据进入模型，且数据有什么风吹草动，拟合模型就跟着剧烈变化；

- variance描述的是模型在**测试集**上的表现，low variance --- 简化模型，减少参数，但容易造成欠拟合 high bias；

综上，bias和variance的选择是一个权衡问题。误差是我们需要关注的，由下面公式表示：
$$
Error = Bias + Variance
$$
随着模型的复杂度上升，预测误差的表现如下：

![2020-07-21-ML-overfit-regularization-2](/assets/machine learning/2020-07-21-ML-overfit-regularization-2.png)

所以，我们要找到一个合适的模型复杂度来换取较优的预测误差。

Bias & Variance 总结

-  增加训练样本—— 解决高方差（overfit）
-  减少特征数量—— 解决高方差
- 增加特征数量—— 解决高偏差
- 增加多项式特征—— 解决高偏差
- 减少正则化程度 $\lamda$ —— 解决高偏差
- 增加正则化程度 $\lamda$ —— 解决高方差

## 正则化

正则化技术其实就是在损失函数中加入罚项，从而降低参数的大小，控制模型复杂度，从而防止过拟合。表现形式：
$$
J(w;x,y) + \alpha \Omega(w) \\
J(w;x,y) - 损失函数 \ ； w-为参数 \\
\alpha :constant - 控制正则化强度 \\
\Omega-惩罚项，通常是L1或L2范数
$$
L1正则化：
$$
F(w;x,y) = J(w;x,y) + \alpha ||w||_1
$$
L2正则化：
$$
F(w;x,y) = J(w;x,y) + \alpha ||w||_2
$$
为什么正则化可以防止过拟合？

当模型的复杂度上升意味着`w`参数较多，从而造成过拟合

如果我们适当减少`w`数量，增加bias 减少variance 换泛化程度；所以让$w=(w_1,w_2,\cdots,w_n)^T$中某些$w_i$为0，某些不为0.由于使用L0范数（不是一个凸优化问题）不能求解，所以转换为L1或者L2正则化问题，让w中某些参数尽可能接近0,以L1正则为例可以得到：
$$
|w_1|+|w_2|+\cdots+|w_n| \leq C
$$
这就转化成一个优化问题：
$$
\begin{cases} 
min J(w;x,y) \\
s.t. ||w||_1 \leq C
\end{cases}
$$
构造拉格朗日函数：
$$
L(w,a) = J(w;x,y)+a(||w||_1-C)
$$
对其求导，假设$w',a'$为最优解：
$$
L'(w,a) = 0 \Rightarrow min \ J(w';x,y)+ min \ a(||w'||_1-C) \\
\Rightarrow min_w J(w;x,y)+ a'||w||_1-a'C \\
正比于 min_w J(w;x,y)+ a'||w||_1
$$
该式子就是L1正则化，从而只要求出L1正则化最小值，就能防止过拟合。

并且L1能够得到稀疏解，话句话说，L1能使较多参数为0，可以做特征选择的原因：

从几何角度,$|w_1|+|w_2|\leq C$ 可以得到菱形：

![2020-07-21-ML-overfit-regularization-3](/assets/machine learning/2020-07-21-ML-overfit-regularization-3.png)

由于菱形有棱有角，更容易在坐标轴上与等高线相交，得到交点最优解。

而L2正则能够防止过拟合也是通过拉格朗日函数求最优解的方法求证，但是L2正则不容易得到稀疏解：

从几何角度，$w_1^2+w_2^2 \leq C^2$ 可以得到一个圆形

![2020-07-21-ML-overfit-regularization-4](/assets/machine learning/2020-07-21-ML-overfit-regularization-4.png)

圆形和等高线在坐标轴上相切会比较难。


$$

$$

