---
layout: post
title: "自动挖掘策略规则"
date: 2022-08-07
description: "自动生成策略规则"
tag: 风控
katex: true
---

## 决策树自动挖掘规则

在挖掘风控策略时，基于经验进行提取有效特征，将几个特征进行组合，决策树可以自动挖掘多条规则，辅助人工筛选。

首先需要定义Target，比如将逾期用户视为1（正样本）非逾期用户为0（负样本），投诉用户视为1（正样本）。

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from sklearn.tree import DecisionTreeClassifier,plot_tree
from sklearn.tree import _tree

# 定义X, y
X = train.loc[:,'XINGBIE':'DKLL']
y = train['label']

# 规则提取算法
def Get_Rules(clf,X):
    n_nodes = clf.tree_.node_count
    children_left = clf.tree_.children_left
    children_right = clf.tree_.children_right
    feature = clf.tree_.feature
    threshold = clf.tree_.threshold
    value = clf.tree_.value

    node_depth = np.zeros(shape=n_nodes, dtype=np.int64)
    is_leaves  = np.zeros(shape=n_nodes, dtype=bool)
    stack = [(0, 0)]

    while len(stack) > 0:

        node_id, depth = stack.pop()
        node_depth[node_id] = depth

        is_split_node = children_left[node_id] != children_right[node_id]

        if is_split_node:
            stack.append((children_left[node_id],  depth+1))
            stack.append((children_right[node_id], depth+1))
        else:
            is_leaves[node_id] = True  
    feature_name = [
            X.columns[i] if i != _tree.TREE_UNDEFINED else "undefined!"
            for i in clf.tree_.feature]

    ways  = []
    depth = []
    feat = []
    nodes = []
    rules = []
    for i in range(n_nodes):   
        if  is_leaves[i]: 
            while depth[-1] >= node_depth[i]:
                depth.pop()
                ways.pop()    
                feat.pop()
                nodes.pop()
            if children_left[i-1]==i:#当前节点是上一个节点的左节点，则是小于
                a='{f}<={th}'.format(f=feat[-1],th=round(threshold[nodes[-1]],4))
                ways[-1]=a              
                last =' & '.join(ways)+':'+str(value[i][0][0])+':'+str(value[i][0][1])
                rules.append(last)
            else:
                a='{f}>{th}'.format(f=feat[-1],th=round(threshold[nodes[-1]],4))
                ways[-1]=a
                last = ' & '.join(ways)+':'+str(value[i][0][0])+':'+str(value[i][0][1])
                rules.append(last)

        else: #不是叶子节点 入栈
            if i==0:
                ways.append(round(threshold[i],4))
                depth.append(node_depth[i])
                feat.append(feature_name[i])
                nodes.append(i)             
            else: 
                while depth[-1] >= node_depth[i]:
                    depth.pop()
                    ways.pop()
                    feat.pop()
                    nodes.pop()
                if i==children_left[nodes[-1]]:
                    w='{f}<={th}'.format(f=feat[-1],th=round(threshold[nodes[-1]],4))
                else:
                    w='{f}>{th}'.format(f=feat[-1],th=round(threshold[nodes[-1]],4))              
                ways[-1] = w  
                ways.append(round(threshold[i],4))
                depth.append(node_depth[i]) 
                feat.append(feature_name[i])
                nodes.append(i)
    return rules

# 决策树模型
clf = DecisionTreeClassifier(max_depth=10, min_sample_leaf=50)
clf = clf.fit(X, y)
rules = Get_Rules(clf, X)

# 讲规则转化为表格
lst_rules = []
lst_0 = []
lst_1 = []
for i in Rules:
    a = i.split(':')
    lst_rules.append(a[0])
    lst_0.append(a[1])
    lst_1.append(a[2])

dic = {'Rules':lst_rules,'Good':lst_0,'Bad':lst_1}
df_rules = pd.DataFrame(dic)
df_rules['Bad'] = df_rules['Bad'].astype('float')
df_rules['Good'] = df_rules['Good'].astype('float')
df_rules['rate'] = df_rules['Bad']/(df_rules['Good']+df_rules['Bad'])
```

# 关联规则自动挖掘策略

对于文本类数据，关联规则会更好用。

输入：文本等事件序列； 输出：有效的风控策略。

## 实施步骤

![实施步骤](\assets\risk\2022-08-12-risk-auto_strategy\1.png)

1. 数据预处理，把行为、数字等特征处理成文本类型的特征；

   - **文本特征分词：**对于文本数据，可以使用分词，将长文本分割成最小信息单元（可以是词维度或者字维度，也可以转换成拼音后分割到拼音维度）；

   - **属性特征序列化：**一些商家的特征，商家等级，商家类型等表示分类属性的特征，要进行序列化处理。比如商家类型为0，商家等级为2，是否个体工商户为1这些原始特征数据可以进行序列化：s_type_0，s_star_2，s_com_1；
   - **数字特征文本话：**针对数字特征，需要进行分层，比如开店时长，0-7天第0层，7-15天第1层等进行离散化。比如商家开店时长12天，注册时长90天，微信支付账号关联用户9位，讲这些数据文本化：kd_1，zc_5，WechatPay_9；
   - **异常行为文本化：**用户的很多异常行为也是重要的序列特征，需要进行挖掘和整理，提高我们策略的丰富程度和准确性。比如沉睡用户苏醒,未缴纳保证金,商品商家间隔短。

   对这些处理后的行为序列进行组合，形成用户的行为序列。

2. 利用关联规则，寻找黑样本（Target为1的正样本）中存在的的高频组合；

   通过关联规则算法挖掘出高频的组合（itemsets），但这仅仅是频繁项集，频繁并不代表准确率高；因此做下一步准确性的验证。

3. 评估每个组合的**历史大盘违规表现**（利用正则表达式进行匹配）；

   计算某个策略大盘总的命中次数，例如策略 **('L1_213', 'L2_344', 's_com_1', '】', '一次')** 过去90天大盘命中的样本总共有1000个，其中800个是黑样本，则该策略的准确率为80%，符合高危策略，按照此方法，依次计算每条策略的的准确率。从而挑选出高准确率的方策略。

   通过正则的方法进行评估,可以使用SQL的方式，大概形式如下：

   | 风控策略 | Target | 标题内容 | 是否命中 |
   | -------- | ------ | -------- | -------- |
   | A        | 1      | ...      | 1        |
   | A        | 0      | ...      | 1        |
   | A        | 1      | ...      | 1        |

   然后进行整理：

   | 策略规则 | 大盘总量（90天总量） | 90天违规量 | 9违规率 |
   | -------- | -------------------- | ---------- | ------- |
   | A        | 1200                 | 600        | 50%     |
   | B        | 800                  | 200        | 25%     |
   | C        | 1000                 | 900        | 90%     |

4. 部署 高频+高违规率 的组合到线上；

5. 上线后的策略动态评估，优胜劣汰。







