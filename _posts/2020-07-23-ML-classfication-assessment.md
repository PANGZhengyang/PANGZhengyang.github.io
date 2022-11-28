---
layout: post
title: "分类模型评价指标"
date: 2020-07-23
description: "机器学习的一些基本概念笔记"
tag: 机器学习
katex: true
---

## 混淆矩阵

|          |  Predicted 0   |  Predicted 1   |
| :------: | :------------: | :------------: |
| Actual 0 | True Negative  | False Positive |
| Actual 1 | False Negative | True Positive  |

1. 准确率
   $$
   Accuracy = \frac{TP+TN}{TP+FP+TN+FN}
   $$
   
2. 精准率：预测为1的样本中实际为1的比率
   $$
   Precision=\frac{TP}{TP+FP}
   $$
   
3. 召回率：实际为1的样本中预测正确的比率（TPR）
   $$
   Recall = \frac{TP}{TP+FN}
   $$

4. 特异性：实际为0的样本中预测正确的比率
   $$
   Specificity=\frac{TN}{FP+TN}
   $$

5. 误报率：`1-Specificity` = `FPR`  实际为0的预测错误的比率
   $$
   FPR = \frac{FP}{FP+TN}
   $$

代码实现：

```python
from sklearn.metrics import confusion_matrix

confusion = confusion_matrix(y_test, model_predict)
# 输出一个2*2的数组，其中行对应真实值，列对应预测值
```


## F1-Score

分类器产生的结果通常是一个概率值，不是直接的0/1变量，通常概率值越大，代表正例的可能性越大。

根据任务的不同也会采取不同的`cutoff value`，大于则为正例，小于则为反例。如重视`Recall`，则`cutoff value`可以设置低一些；而重视`Precision`，`cutoff value`可以设置高一些。

对于选择合适的`cutoff value`,通常可以看`F1-Score`
$$
F1 = 2\frac{Precision*Recall}{Precision+Recall}
$$
`F1-Score`越大，我们选择的`cutoff value`越好。

代码实现：

```python
from sklearn.metrics import f1_score

f = f1_score(y_test, model_predict)
```



## ROC

在未设定`cutoff value`或任务不明确第情况下，我们可以使用`ROC`曲线评价一个分类模型效果的好坏。

ROC曲线描绘的是不同的`cutoff value`情况下，以`TPR`为纵轴，`FPR`为横轴，描述随着`cutoff value`变小，`TPR` 和 `FPR` 的变化。在计算上，选取不同的`cutoff value`可以得到`TPR、FPR`，从而做ROC图。

如果是随机分类，没有进行任何学习器，FPR=TPR，即正例分对和负例分错概率相同，预测出来的正例负例和正例负例本身的分布是一致的，所以是一条45°的直线。

**因此，ROC曲线越向上远离这条45°直线，说明用了这个学习器在很小的代价（负例分错为正例，横轴）下达到了相对较大的召回率（TPR）**

代码实现：

```python
from sklearn.metrics import roc_curve

fpr, tpr, thresholds = roc_curve(y_true, y_score, pos_label)
'''
主要参数：
- y_true:真实的样本标签，默认为{0，1}或者{-1，1}。如果要设置为其它值，则 pos_label 参数要设置为特定值。例如要令样本标签为{1，2}，其中2表示正样本，则pos_label=2。
- y_score:对每个样本的预测结果。
- pos_label:正样本标签

返回值：
- fpr
- tpr
- thresholds:阈值
'''
plt.plot(fpr, tpr, label='ROC Curve')
```

例子：

```python
import numpy as np
from sklearn import metrics

y = np.array([0, 0, 1, 1]) # 真实值
scores = np.array([0.1, 0.4, 0.35, 0.8]) # 预测概率
fpr, tpr, thresholds = metrics.roc_curve(y, scores, pos_label=1) # 1 为positive

fpr
# array([0, 0.5, 0.5, 1])
tpr
# array([0.5, 0.5, 1, 1])
thresholds
# array([0.8, 0.4, 0.35, 0.1])
```

对上述过程进行解释，首先会选取预测概率中最大的值作为cut-off value，如果预测概率大于等于cut-off value，则预测为positive。所以第一个会选取0.8作为thresholds。

根据混淆矩阵：

|      |   P0   |   P1   |
| :--: | :----: | :----: |
|  A0  | TN = 2 | FP = 0 |
|  A1  | FN = 1 | TP = 1 |

所以：

TPR = TP / (TP+FN) = 0.5

FPR = FP / (FP+TN) = 0

然后thresholds = 0.8，得到一组TPR 和FPR，以此内推，当thresholds=0.4时，能够在得到一组，画图即可。

## AUC 

AUC(area under ROC curve),ROC 曲线包围的面积，如果越大，模型越好。

在风控中0.65以上就比较好。

代码实现：

```python
from sklearn.matrics import roc_auc_score

model_auc = roc_auc_score(y_test, y_score)
```



## KS

广泛用于风控领域，表示模型将正负样本区分开的能力。值越大，表示模型计算出的预测值对好坏用户进行区分的能力越强。一般来说，KS>0.2即可认为模型具有较好的预测准确性；如果超过0.5需要注意，是不是过拟合了。

类比于ROC曲线：

KS的横轴是不同阈值（thresholds），纵轴是TPR 和 FPR，KS值为max [abs(TPR - FPR)]

计算方法：（good = 0， bad = 1）

- 逻辑回归跑出的概率，从小到大排序（逻辑回归的概率越小越好）
- 切bins
- 计算每个bin的TPR、FPR
- 计算max [(TPR - FPR) 绝对值]

确定cutoff value最有效的就是看 bad rate的趋势
$$
风险倍数 = \frac{每个bin的bad \ rate}{整体的bad \ rate}
$$
一般做好模型，就能在尾部找到3.5倍左右的风险倍数，一般3倍以上的风险就可以拒绝了，实在找不到风险倍数高的， 2倍，2.5倍数也能接受 。

## 洛伦兹曲线、GINI系数

将信用评分从高到低进行排序，横轴为累计人数比列，纵轴为TPR（累计坏样本比例）

洛伦兹曲线的弧度越大，GINI越大，这个模型区分好坏样本能力越强
$$
GINI = \frac{A}{A+B}
$$


## 模型稳定性

群体稳定性指标PSI（Population Stability Index）衡量模型的预测值与实际值的偏差大小

$$
PSI = \sum_{i=1}^{n} (A_i - E_i) \times \ln \frac{A_i}{E_i}
$$

其中，$A_i$表示实际占比(Actual)，这里使用测试集数据；$E_i$表示预期占比(Expected)，这里使用建模样本。

| Score     | A%   | E%   | A - E | A / E  | PSI    |
| --------- | ---- | ---- | ----- | ------ | ------ |
| 0 - 200   | 20%  | 10%  | 10%   | 0.875  | 0.0013 |
| 200 - 300 | 30%  | 20%  | 10%   | 0.800  | 0.0045 |
| 300+      | 50%  | 70%  | -20%  | 1.1111 | 0.0011 |
| Total     |      |      |       |        | 0.0069 |

 PSI 数值越小，两个分布之间的差异就越小，代表越稳定。

|      PSI      | 模型稳定性 |
| :-----------: | :--------: |
|   [0, 0.1)    |     好     |
|  [0.1, 0.25)  |    一般    |
| [0.25,正无穷) |   不稳定   |

## Lift曲线

提升度（Lift）衡量 模型/规则 对目标“响应”的预测能力 高于随机选择多少倍。Lift大于1，表示模型/规则比随机捕捉了更多的“响应”。

计算公式：

$$
Lift = \frac{TP / (TP+FP)}{(TP+FN)/(TP+FP+TN+FN)}
$$

Lift指标可以这样理解：在不使用模型或者策略的情况下，我们用先验概率估计正例的比例，即上式分母部分；利用模型后，我们不需要从整个样本中来挑选正例，只需要从我们预测为正例的样本中挑选正例。

| Decile | Obs  | Bad  | Bad(%) Captured by model | Bad(%) Captured by randomly | Cumulative Bad(%) by model | Cumulative Bad(%) randomly | Lift |
| :----: | :--: | :--: | :----------------------: | :-------------------------: | :------------------------: | :------------------------: | :--: |
|   1    | 100  |  20  |       20/55 = 36%        |             10%             |            36%             |            10%             | 3.60 |
|   2    | 100  |  10  |           18%            |             10%             |            54%             |            20%             | 2.70 |
|   3    | 100  |  8   |           15%            |             10%             |            69%             |            30%             | 2.30 |
|   4    | 100  |  6   |           11%            |             10%             |            80%             |            40%             | 2.00 |
|   5    | 100  |  5   |            9%            |             10%             |            89%             |            50%             | 1.78 |
|   6    | 100  |  3   |            5%            |             10%             |            94%             |            60%             | 1.57 |
|   7    | 100  |  2   |            4%            |             10%             |            98%             |            70%             | 1.40 |
|   8    | 100  |  1   |            2%            |             10%             |            100%            |            80%             | 1.25 |
|   9    | 100  |  0   |            0%            |             10%             |            100%            |            90%             | 1.11 |
|   10   | 100  |  0   |            0%            |             10%             |            100%            |            100%            | 1.00 |
| Total  | 1000 |  55  |                          |                             |                            |                            |      |

Decile 表示分数段，Obs 表示不使用评分卡，用随机选择方法覆盖到的坏客户占比，等价于该组观测数占总观测数的比例（分子分母同时乘以样本整体的坏账率）。Bad 表示使用评分卡得到的坏客户占比。