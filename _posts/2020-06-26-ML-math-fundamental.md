---
layout: post
title: "机器学习相关数学知识"
date: 2020-06-26
description: "机器学习的一些基本概念笔记"
tag: 机器学习
katex: true
---

## 导数

在一元函数的情况下，导数就是函数的变化率，且只存在一个方向的变化率，从几何意义上看：就是某一点切线的斜率。

基本求导公式如下：[点击查看更多](https://blog.csdn.net/xueruixuan/article/details/78780105?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522163754972316780271963384%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=163754972316780271963384&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-78780105.first_rank_v2_pc_rank_v29&utm_term=%E5%AF%BC%E6%95%B0%E5%85%AC%E5%BC%8F&spm=1018.2226.3001.4187)

$$
(C)' = 0 \\
(x^u)' = ux^{u-1} \\
(e^x)' = e^x \\
(\ln(x))' = \frac{1}{x} \\
(\log_ax)' = \frac{1}{x\ln(a)}
$$

基本求导法则如下：设 $u = u(x), \ v = v(x)$都可导

$$
(u \pm v)' = u' \pm v' \\
(Cu)' = Cu' \\
(uv)' = u'v+uv' \\
(\frac{u}{v})' = \frac{u'v+uv'}{v^2}
$$

## 偏导数

在多元函数的情况下，几何图形可能是一个曲面，在曲面上的一点可以做出无数条曲线，每根曲线都可能做出一根过该点的切线，此时有无数多个方向的变化率。

如果要求沿x轴方向的变化率，对x求偏导即可。

## 方向导数

上述除了坐标轴方向的变化率，还有很多方向的变化率，对于任意方向的变化率我们称为方向导数。

## 梯度

方向导数中取到最大值得方向就是梯度方向。梯度的值是方向导数的最大值。

## 梯度下降

梯度下降是寻找模型参数使得Cost function最小值的算法。

想象身处在连绵不断的山脉，要下山，怎么下山最快呢？我们先环顾四周，寻找最陡峭的方向，然后往下跨。这是梯度下降一个真是的写照。梯度下降先随机找一组参数就得cost function的值，然后寻找下一组参数能够让cost function下降最多。持续这么做，直到一个局部最优解。

## 矩阵求导

矩阵基础运算可以[看这篇的最后部分](待写)

矩阵求导的本质：**矩阵A中的每个元素对矩阵B中的每个元素进行求导**。

比较重要的两个口诀：(Y X拉伸术)

- 标量不变，向量拉伸；
- 前面横向拉，后面纵向拉

几个常见矩阵求导公式：

$$
\frac{dA^TX}{dX} = A
$$

推导：A,X均为列向量 $A = [a_1,a_2,\cdots , a_n]^T$以及$X=[x_1,x_2,\cdots,x_n]^T$

$$
f(X) = A^TX = \sum_i^na_ix_i
$$

f(x)为标量不变，x为向量拉伸：后面纵向拉伸

$$
\frac{df(X)}{dX}= 
\begin{bmatrix}\frac{\partial f(X)}{\partial x_1}\\
\frac{\partial  f(X)}{\partial x_2}\\
\vdots\\
\frac{\partial  f(X)}{\partial x_n}
\end{bmatrix} = 
\begin{bmatrix}a_1\\
a_2\\ 
\vdots\\
a_n
\end{bmatrix}
=A
$$

第二个比较重要的是：

$$
\frac{dX^TAX}{dX} = AX+A^TX
$$

## 泰勒展开式

泰勒展开式就是把一个三角函数或者指数函数或者其他比较难缠的函数用多项式替换掉。

也就是说，有一个**原函数** $f(x)$，我再造一个图像与原函数图像相似的**多项式函数** $g(x)$ ,为了保证相似，我只需要保证这俩函数在某一点的**初始值相等，1阶导数相等，2阶导数相等，……n阶导数相等**。
