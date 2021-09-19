---
layout: post
title: "统计学习"
date: 2020-10-03
description: "概率论与数理统计"
tag: 统计
katex: true
---

##  描述性统计

### 直方图(histogram)

对于数值型数据，描述数据分布形态：通常要切bin

### 偏度


$$
skew = E[(\frac{X - \mu}{\sigma})^3]
$$

即3阶中心距。

![](/assets/2021-08-03-statistical-theory-1.png)

对于偏度的理解：这里的“偏” 指的是`偏离` 的意思，左偏就是偏离左边，数据集中在右边所以有：
$$
mean < median<mode \\
mode:数据的集中趋势 \\
mean：描述正态的对称轴
$$

### 峰度

$$
kurt = E[(\frac{X - \mu}{\sigma})^4]
$$

即4阶中心距。

![](/assets/2021-08-03-statistical-theory-2.png)

### 交叉分组(crosstab)

crosstab是汇总两个变量数据的方法。

### 几种常见的平均数

1. 算术平均

   $$
   \frac 1 n \sum_{i=1}^{n} x_i
   $$

2. 几何平均

   $$
   \sqrt[n] {\prod_{i=1}^{n} x_i}
   $$

3. 加权平均

   $$
   \sum_{i=1}^{n} x_i \omega_i / \sum_{i=1}^{n} \omega_i
   $$

4. 调和平均

   调和平均是值的倒数的算术平均的导数。易受极端值的影响，且受极小值的影响比受极大值的影响更大。

   $$
   \frac 1 {\frac 1 n \sum_{i=1}^{n} \frac 1 {x_i}} = \frac n {\sum_{i=1}^{n} \frac 1 {x_i}}
   $$

> 算术平均 $\geq$ 几何平均 $\geq$ 调和平均

### 百分位数

$$
第p百分数位置：\\
L_p = \frac{p}{100}(n+1) ,\ \ n为有多少个数据
$$

$Q_1$ 第一四分位数 $Q_2$ 中位数 $Q_3$第三四分位数

四分位数间距 ：
$$
IQR = Q_3-Q_1
$$

 ### 箱型图

![](/assets/2020-10-03-statistical-theory-base1.png)

虚线末尾表示原始数据最大值和最小值

箱型图排查异常值的原理就是：划出上限：`$Q_3$+1.5IQR` 和下限`$Q_1$-1.5IQR`

## 统计

概率论是站在上帝视角，知道总体分布来研究问题；而在实际生活中，我们可能没有“上帝之眼”，这个时候统计就非常重要了，我们可以根据样本来推断总体。

**参数**是总体的数字特征，而**统计量**是样本的数字特征，我们需要用统计量去推断参数，用样本去代表总体。

## 分布

### 二项分布

$$
B(n, p) \\
EX = np, DX = np(1 - p)
$$

### 泊松分布

$$
P(x) = \frac{\lambda^x e^{-\lambda}}{x!}, x = 0, 1, 2, ... \\
EX = \lambda, DX = \lambda
$$

### 正态分布

$$
f(x) = \frac 1 {\sqrt{2\pi} \sigma} e^{-\frac{(x - \mu)^2}{2 \sigma^2}}, x \in \mathbb{R} \\
EX = \mu, DX = \sigma^2
$$

### 指数分布

$$
f(x) =
\begin{cases}
\lambda e^{-\lambda x}, x>0 \\
0, x \le 0
\end{cases}
$$

或

$$
f(x) =
\begin{cases}
\frac 1 \theta e^{-\frac x \theta}, x>0 \\
0, x \le 0
\end{cases}
\quad \theta = \frac 1 \lambda
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

### 卡方分布

若$X_1, ..., X_n$相互独立，且$X_i \sim N(0, 1)$，则

$$
\sum^n_{i=1} X^2_i \sim \chi^2 (n) \\
EX = n, DX = 2n
$$

### t分布

$$
EX = 0, DX = \frac n {n - 2}
$$

若$X \sim N(0, 1)$，$Y \sim \chi^2(n)$，$X$与$Y$相互独立，则

$$
t = \frac X {\sqrt{\frac Y n}}
$$

若$X \sim N(\mu, \sigma^2)$，则

$$
\bar X = \frac 1 n \sum^n_{i=1} X_i \\
E \bar X = \frac 1 n \sum EX_i = \mu \\
D \bar X = \frac 1 {n^2} \sum DX_i = \frac {\sigma^2} n
$$

因此：

$$
\frac {\bar X - \mu}{\frac \sigma {\sqrt n}} \sim N(0, 1)
$$

又有如下的推导存在：

$$
s^2 = \frac 1 {n - 1} \sum^n_{i=1} (X_i - \bar X)^2 \\
\begin{aligned}
\frac {(n-1)s^2}{\sigma^2} &= \frac {\sum(X_i - \bar X)^2}{\sigma^2} = \sum (\frac{X_i}{\sigma} - \frac{\bar X}{\sigma})^2 \\
&= \sum (\frac{X_i - \mu}{\sigma} - \frac{\bar X - \mu}{\sigma})^2 \\
&= \sum (Z_i - \bar Z)^2 \\
&= \sum (Z_i^2 - 2Z_i \bar Z + \bar Z^2) \\
&= \sum^n_{i=1} Z^2_i - n\bar Z^2 \sim \chi^2(n - 1)
\end{aligned}
$$

故

$$
\frac {\frac {\sqrt n (\bar X - \mu)} \sigma} {\sqrt{\frac{\frac{(n-1)s^2}{\sigma^2}}{n - 1}}} = \frac {\sqrt n (\bar X - \mu)} s \sim t(n-1)
$$

### F分布

若$Y$与$Z$相互独立，$Y \sim \chi^2(m)$，$Z \sim \chi^2(n)$，则

$$
X = \frac {\frac Y m}{\frac Z m} \sim F(m, n) \\
EX = \frac n {n - 2} \\
DX = \frac {2 n^2(m+n-2)} {m(n-2)(n-4)}, n>4
$$

### 抽样

如何选取样本？——抽样！

1. 从有限总体的抽样

   使用概率抽样，最常用的就是随机数~

2. 从无限总体的抽样

   抽取一个**随机样本**:

   - 抽取的每个个体都来自同一个总体；
   - 每个个体的抽样都是独立的。

### 点估计

1. 样本均值$\overline x$是总体均值$mu$的点估计；

2. 样本标准差 $s$是总体标准差$\sigma$的点估计；

3. 样本比率$\overline p$是总体比率$p$的点估计

### 抽样分布

从总体当中抽出一个样本可以得到样本均值，样本标准差；那多次抽出样本，也可以得到多个样本均值，且样本均值的取值也有各种可能的结果，我们可以称样本均值的分布为抽样分布（$\overline x$所有可能值得概率分布）。

所以$\overline x$也有期望，且
$$
E(\overline x) = \mu
$$
所以$\overline x$的方差：
$$
D(\overline x) = D(\frac{1}{n}\sum^{n}_{i=1}x_i) = \frac{1}{n^2}D(x_1+...+x_n)=\frac{\sigma^2}{n}
$$
所以样本方差的期望：

$x_i$ 是来自总体的样本，$\overline X$ 是该样本的均值
$$
E(S^2) = E[\frac{1}{n-1}\sum_{i=1}^{n}(x_i-\overline X)^2]=\frac{1}{n-1}E[(\sum_{i=1}^{n}x_i^2-2\sum_{i=1}^{n}x_i\overline X+n\overline X^2)] 

\\
=\frac{1}{n-1}E[(\sum_{i=1}^{n}x_i^2-2n\frac{1}{n}\sum_{i=1}^{n}x_i\overline X+n\overline X^2)]=
\frac{1}{n-1}E[(\sum_{i=1}^{n}x_i^2-2n\overline X^2+n\overline X^2)] 

\\
=\frac{1}{n-1}E[\sum_{i=1}^{n}x_i^2-n\overline X^2] = \frac{1}{n-1}[nE(X^2)-nE(\overline X^2)] 

\\ 
\because D(X) = E(X^2) - E^2(X) \Rightarrow E(X^2) = D(X)+ E^2(X) = \sigma^2 + \mu^2 

\\
\because D(\overline X) = E(\overline X^2) - E^2(\overline X) \Rightarrow E(\overline X^2) = D(\overline X) +E^2(\overline X) = \frac{\sigma^2}{n} +\mu^2 

\\
代入上式:E(S^2) =   \frac{1}{n-1}[n\sigma^2+n\mu^2-\sigma^2-n\mu^2] = \sigma^2
$$
几个概念的补充：

1. 自由度：互相**独立**变量的个数
2. 样本标准差分母是`n-1`：校正样本均值$\overline x$所减少的自由度，样本数据本身没有偏差，是引入了新信息（样本均值）让结果出现了偏差：比如两个数a，b，a=1 但你并不知道b等于多少，这说明这两个数是独立的自由的，但是引入均值后：两个数的均值$\overline x$为2,那你就可以倒推b为3，这个时候两个数之间就不再是独立的了，自由度也由原来的2变为了1；所以在计算样本方差时，引入均值，导致自由度会减少1，所以需要去修正，即用`n-1`

**样本比例的抽样分布**

假设总体是班上有N个学生，其中$N_0$个男生，则男生的（总体）比例为$p = \frac{N_0}{N}$,现在从这个版抽出n个学生，在这个样本里面有x个男生，则样本比例为$\frac{x}{n}$，其实它符合**二项分布**，故设`X=样本中抽到的男生数`,X~B(n,p)
**样本量足够大时，二项分布趋于正态分布**
$$
\overline p = \frac{x}{n} \\
E(\overline p) = np.\frac{1}{n} = p \\
D(\overline p) = np(1-p) .\frac{1}{n^2} = \frac{p(1-p)}{n}
$$

在选取统计量时，满足下面三个特征：

- 无偏性：样本统计量的期望等于总体均值
- 有效性：谁的标准差小谁就越有效
- 一致性：随着样本容量的增大，点估计量的值与总体参数越来越接近

### 区间估计

置信区间：以`1-a`的把握保证总体参数落到的区间
置信度：`1-a` 其中`a`表示显著性水平

几个比较重要的定理,上面的分布已经证明过
$$
\overline X \sim N(\mu, \frac{\sigma^2}{n}) \Rightarrow \frac{\overline X - \mu}{\frac{\sigma}{\sqrt n}} \sim N(0,1) \\
\frac{\overline X - \mu}{\frac{s}{\sqrt n}} \sim t(n-1) \\
\frac{(n-1)s^2}{\sigma^2} \sim \chi^2(n-1)
$$

总体均值区间估计：

- 当$\sigma$已知：
  $$
  \overline X \pm z_\frac{a}{2}{\frac{\sigma}{\sqrt n}}
  $$
  
- 当$\sigma$未知：
  $$
  \overline X \pm t_\frac{a}{2}{\frac{s}{\sqrt n}}
  $$

总体比例的区间估计：

因为$\overline p$抽样分布服从正态分布，且用$z_\frac{a}{2} \sigma$作为边际误差，但$p$未知不能直接使用$\sigma$，所以用$\overline p$代替$p$
$$
\overline p \pm z_\frac{a}{2} \sqrt{\frac{\overline p(1-\overline p)}{n}}
$$


