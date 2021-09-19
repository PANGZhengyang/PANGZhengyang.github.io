---
layout: post
title: "概率论学习笔记"
date: 2020-09-11
description: "概率论与数理统计"
tag: 统计
katex: true 
---

# 概率论

相较于统计，概率论是站在“上帝的视角”，在总体分布已知的情况下研究问题。

## 排列组合

$$
全排列:n! \\
排列:P_{n}^{m} = \frac{n!}{(n-m)!} \\
组合:C_{n}^{m} = \frac{n!}{m!(n-m)!} \\
P_{n}^{m} = C_{n}^{m} *m!
$$

## 事件及概率

$$
P(A \cup B) = P(A)+P(B) - P(AB)
$$

### 条件概率

事件A发生的条件下B发生的概率，此时样本空间已经由S变为A
$$
P(B | A) = \frac{P(A \cap B)}{P(A)}
$$

### 独立和互斥

互斥：一事件发生另一事件将不发生，可以试用文氏图表示
$$
P(AB) = P(\varnothing) = 0
$$


独立：一事件的发生不会影响另一事件发生的概率，不可以使用文氏图表示
$$
P(AB) = P(A)P(B)
$$

$$
如果P(A)>0,P(B)>0,则互斥和独立不能同时发生 \\
独立:P(AB) = P(A)P(B) >0 \\
互斥:P(AB) = P(\varnothing) = 0
$$

### 全概率与贝叶斯

全概率是：由“因”求“果”

贝叶斯是：执“果”索“因”

以一个例子来说明这两个的区别：

> 一个村子有3个小偷，小红A1，小明A2，小英A3；记事件$A_1$为小红去偷以此类推；事件B为村子被盗
>
> (1) 求村子被盗的概率$\longrightarrow$ 知道原因求结果，使用全概率
> $$
> P(B)=P(A_1B)+P(A_2B)+P(A_3B)=P(A_1)P(B|A_1)+P(A_2)P(B|A_2)+P(A_3)P(B|A_3)
> $$
> (2)现在已经知道村子被盗，求是小红偷的概率$\longrightarrow$知道结果去探索原因：使用贝叶斯
> $$
> P(A_i|B)=\frac{P(A_iB)}{P(B)}=\frac{P(A_i)P(B|A_i)}{\sum_jP(A_j)P(B|A_j)}=\frac{某一个原因造成的结果}{所有原因共同的结果}
> $$
> 



## 随机变量

随机变量其实是一个函数，定义在样本空间，取值于实数轴上的函数

### 离散型随机变量 $ \ longrightarrow$  概率函数：分布律

#### 两点(0-1)分布 - 伯努利分布

$$
P\{X = k\} = p^k \cdot (1-p)^{1-k} , k = 1,0
$$

#### 二项分布-n重伯努利试验

$$
B(n, p) \\
EX = np, DX = np(1 - p)
$$

> 当np >= 5且n(1-p)>=5 二项分布趋于正态分布

#### 泊松分布

$$
P\{x =k \} =\frac{\lambda^k \cdot e^{-\lambda}}{k!}=\frac{\mu^k \cdot e^{-\mu}}{k!}
\\
EX = \lambda, DX = \lambda
$$

$\lambda$指强度，是热热闹闹还是冷冷清清；

泊松分布描述的是一个随机性：某场合某单位时间源源不断的质点来流的个数。比如8点到9点到麦当劳的人数。

当事件满足下面两个性质，随机变量服从泊松分布：

- 在任意两个相等长度区间上，事件发生的概率相等
- 时间在某一区间上是否发生与事件在其他区间上是否发生是独立的。

### 连续型随机变量 $\longrightarrow$ 概率函数：概率密度函数

关于概率密度函数的几点理解：

1. 概率密度可以类比于一个密度不均的小球，总质量为1（概率为1）
2. 概率密度就相当于这个小球某处的密度，这个`密度`乘上`体积`等于`质量<1` 对于密度越大的点 说明单位体积落在该点的质量越大（发生在这个点附近的时间的概率越大）
3. 概率密度说明的是概率分布问题，其面积才是表示概率

#### 指数分布 - 无记忆性等待型分布

$$
f(x) = \begin{cases}
\lambda e ^{-\lambda x}, \ x>0 \\
0, \ other
\end{cases}
$$

$$
F(x) =
\begin{cases}
1 - e^{-\lambda x}, x>0 \\
0, x \le 0
\end{cases}
$$

$$
EX = \frac 1 \lambda, DX = \frac 1 {\lambda^2}
$$

指数分布通常用来描述寿命：灯泡的寿命、内存条的寿命

$\lambda$ 表示的失效率-机器坏掉率

关于无记忆性：灯泡在使用了1年没坏，在继续和使用一年怪的概率和新的灯泡使用1年坏的概率相同
$$
if \ x \sim Exp(\lambda): P \{X >s+t \ | \ x>s\} = P\{x >t\}
$$

指数分布和泊松分布的联系：

- 泊松分布描述的是每一区间中事件发生的次数
- 指数分布描述的是事件发生的时间间隔程度

#### 正态分布

普遍现象发生概率高，极端现象发生概率低

贵州省男性成年人身高就服从正态分布
$$
f(x) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{(x-\mu)^2}{2\sigma^2}}
$$

## 随机变量的特征

### 期望

刻画了x取值的平均水平
$$
E(C) = C\\
 E(CX)=CE(X)\\
E(C+X) = C+E(X)
$$


### 方差

刻画了X与Ex的离散程度
$$
D(X) = E\{[X-E(X)]^2\} = E(X^2) - E^2(X)
$$

$$
D(C) = 0\\
D(CX) = C^2D(X)\\
D(C+X) = D(X) \\
D(X+Y) = D(X) + D(Y) +2Cov(X,Y) \\
若X,Y独立: D(X+Y) = D(X)+D(Y)
$$

### 协方差

刻画了X,Y质检的偏差程度
$$
Cov(X,Y) = E\{[X-E(X)][Y-E(Y)]\} = E(XY) - E(X)E(Y)\\
Cov(X,X) = D(x)
$$

### 相关系数

刻画了X,Y之间是否有线性关系

在协方差的基础上消除量纲
$$
\rho = \frac{Cov(X,Y)}{\sqrt{D_X}\sqrt{D_Y}}
$$

## 大数定理

**当样本数量越多，样本的平均值就越接近总体的均值（均值的期望）**

### 伯努利大数定理

这即是频率稳定性的真正含义，当试验次数很大时，便可以用事件的频率来代替概率。
$$
n \longrightarrow \infty ,\ 频数 \xrightarrow{P} 概率
$$

### 切比雪夫大数定理(本质上和辛钦大数定理一样)

均值依概率收敛到均值的期望
$$
\overline X = \frac{1}{n}\sum^{n}_{i=1}x_i \xrightarrow{P}E(\overline X)
$$
体现均值在大样本下的稳定性

## 中心极限定理

设随机变量$X_1, X_2, ..., X_n$ i.i.d，$E(X_k) = \mu$，$D(X_k) = \sigma^2 > 0$，则随机变量之和$\sum_{k=1}^n X_k$的标准化变量

$$
Y_n = \frac {\sum_{k=1}^n X_k - E(\sum_{k=1}^n X_k)} {\sqrt {D(\sum_{k=1}^n X_k)}} = \frac {\sum_{k=1}^n X_k - n\mu} {\sqrt n \sigma}
$$

的分布函数$F_n(x)$对于任意$x$满足

$$
\begin{aligned}
\lim_{n \to \infty} F_n(x) &= \lim_{n \to \infty} P(\frac {\sum_{k=1}^n X_k - n\mu} {\sqrt n \sigma} \le x) \\
&= \int_{-\infty}^x \frac 1 {\sqrt {2\pi}} e^{- \frac {t^2} {2}} dt = \Phi(x)
\end{aligned}
$$

一般情况下，很难求出$n$个随机变量之和的分布函数，上式表明当$n$充分大时，可以用正态分布来近似。上面公式的另一个形式为：

$$
\frac {\sum_{k=1}^n X_k - n\mu} {\sqrt n \sigma} = \frac {\frac 1 n \sum_{k=1}^n X_k - \mu} {\sigma / \sqrt n} \overset{近似地}{\sim} N(0, 1) \\
\overline X = \frac 1 n \sum_{k=1}^n X_k \overset{近似地}{\sim} N(\mu, \sigma^2 / n)
$$

**中心极限定理表明大量的随机变量值和的分布逼近正态分布**

在运用上：<font color = orange>  不管总体分布是什么，样本的平均值总是围绕总体整体的均值附近，并呈正态分布</font>



#### 棣莫弗-拉普拉斯定理

设随机变量$\eta_n$服从参数为$n, p (0 < p < 1)$的二项分布，则对于任意$x$有

$$
\lim_{n \to \infty} P(\frac {\eta_n - np} {\sqrt{np(1-p)}} \le x) = \int_{-\infty}^x \frac 1 {\sqrt {2\pi}} e^{- \frac {t^2} 2} dt = \Phi(x)
$$

这个定理表明，**正态分布是二项分布的极限分布**。

