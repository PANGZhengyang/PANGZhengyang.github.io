---
layout: post
title: "分类模型评价指标"
date: 2020-07-23
description: "机器学习的一些基本概念笔记"
tag: 机器学习
katex: true
---

## 混淆矩阵

|             |    Actual 1    |    Actual 0    |
| :---------: | :------------: | :------------: |
| Predicted 1 | True Positive  | False Positive |
| Predicted 0 | False Negative | True Negative  |

1. 准确率
   $$
   Accuracy = \frac{TP+TN}{TP+FP+TN+FN}
   $$
   
2. 精准率：预测为1的样本中实际为1的比率
   $$
   Precision=\frac{TP}{TP+FP}
   $$
   
   
3. 召回率：实际为1的样本中预测正确的比率
   $$
   Recall = \frac{TP}{TP+FN}
   $$

4. 特异性：实际为0的样本中预测正确的比率
   $$
   Specificity=\frac{TN}{FP+TN}
   $$

5. `1-Specificity` = `FPR` : 实际为0的预测错误的比率
   $$
   FPR = \frac{FP}{FP+TN}
   $$
   

## F1-Score

分类器产生的结果通常是一个概率值，不是直接的0/1变量，通常概率值越大，代表正例的可能性越大。

根据任务的不同也会采取不同的`cutoff value`，大于则为正例，小于则为反例。如重视`Recall`，则`cutoff value`可以设置低一些；而重视`Precision`，`cutoff value`可以设置高一些。

对于选择合适的`cutoff value`,通常可以看`F1-Score`
$$
F1 = 2\frac{Precision*Recall}{Precision+Recall}
$$
`F1-Score`越大，我们选择的`cutoff value`越好。

## ROC

在未设定`cutoff value`或任务不明确第情况下，我们可以使用`ROC`曲线评价一个分类模型效果的好坏。

ROC曲线描绘的是不同的`cutoff value`情况下，以`Recall`为纵轴，`1-specificity`为横轴，描述随着`cutoff value`变小，`Recall` 和 `1-specificity` 的变化。

如果是随机分类，没有进行任何学习器，FPR=TPR，即正例分对和负例分错概率相同，预测出来的正例负例和正例负例本身的分布是一致的，所以是一条45°的直线。

**因此，ROC曲线越向上远离这条45°直线，说明用了这个学习器在很小的代价（负例分错为正例，横轴）下达到了相对较大的召回率（TPR）**

## AUC 

AUC(area under ROC curve),ROC 曲线包围的面积，如果越大，模型越好。

## KS



