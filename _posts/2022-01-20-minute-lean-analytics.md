---
layout: post
title: "精益数据分析"
date: 2022-01-20
description: "精益数据分析读书笔记"
tag: 读书笔记
---

总的来说，本书主要是**围绕不同商业模式的创业公司应该建设什么样的数据指标**来帮助看清公司发展的道路。书的结构比较清晰：分别介绍了为什么要这样做？选择这些指标的依据是什么？选择后要注意什么？

![2022-01-19-overall.png](/assets/reading minutes/lean-analytics/2022-01-19-overall.png)

## Part 1： 为什么精益创业中要引入数据分析？

首先精益创业是什么？

精益创业的本质是使用**最小可行化产品**完成快速测试、快速迭代。话句话说：使用最小的成本代价去寻找最有价值的方向，如果发现方向有价值，再继续深入。而数据分析是量化这一过程的最佳方法。

### 思考

我理解的“创业”其实不止是创立一家新的公司，开展属于自己的事业。“创业”也可以在一家公司里面开展一个项目，优化一项工作。通过这段时间的工作，我发现在做一个项目的时候，也可以采用“精益”的方法：使用最小的代价去寻找有价值的方向。例如：在“异常经办监控”中，我首先为业务设计了指标体系并每日为他们输出监控报表，等待这个监控体系较完善后，与产品、研发、大数据合作将这个体系做成实时看板产品。

“数据分析”这个词可以分两个部分来看，一是通过数据去看清问题，定位问题。二是通过数据去分析问题，提出可行建议。

## Part2：如何确定公司的数据指标？

### 好的数据指标

- 比较性
- 简单易懂
- 比率
- 改变行为

### 指标类型

- **定性指标和定量指标**

  定性指标描述“为什么”，吸收主观因素；定量指标描述“什么”和“多少”，排除主观因素。

- **虚荣指标和可付诸行动的指标**

  数据是否可以让你采取某些行动，指明某些方向。若一个指标，比如总注册用户数、总用户数，总是单调递增的，这类指标不能传达用户行为信息，需要提防虚荣指标。有个方法就是问问自己，这个指标如果变化了可以说明什么问题，能够据此采取某些行动。

  8个常见的虚荣指标包括：点击量、页面浏览量、访问量、独立访客数、粉丝/好友/赞的数量、网站停留时间、收集到的用户邮件地址数量、下载量。以上这些指标并不是本身无意义，而是在随时间发单的过程中，他们往往都呈现’看上去很美‘的单调增。我们要从这些指标再往下去挖掘一步，有哪些**可以转化为对公司有价值**的部分。

- **探索性指标与报告性指标**

  探索性指标是“我们不知道我们不知道的”，它可能转化为未来业务发展的秘密武器，例如facebook中发现的’妈妈圈‘，帮助创建了独立的社交产品。

  报告性指标是“我们知道我们不知道的”，在业务中我们需要持续监控。

- **先见性指标和后见性指标**

  先见性指标：预测未来，比如“潜在用户”；后见性指标：提示问题的存在，比如”流失用户“。

- **相关性指标和因果性指标**

  如果两个指标具有因果关系，那就发现了增长密码！

## Part3：如何找到正确的指标

### 数据分析框架

1. 海盗模型（AARRR) 

   |       要素       |                   功能                   |                           相关指标                           |
   | :--------------: | :--------------------------------------: | :----------------------------------------------------------: |
   | 获客 Acquisition |     通过各种方式博取眼球获取更多用户     | **流量类**（新增）、**获客成本类**（CAC，CPM，CPC）、 **渠道转化率**（衡量渠道质量）、**新用户转化漏斗数据** |
   | 激活 Activation  |     将拉新用户转化为产品真正的参与者     | **活跃类**（DAU，MAU）、**注册用户数**、**订阅量**、**新手教程完成量** |
   |  留存 Retention  |            让用户留！下！来！            |    **留存类**（次留、三留、七留）、据上次登录时间、流失率    |
   |   变现 Revenue   | 商业活动的产出（不同商业模式有不同产出） | **收入类**（广告、增值服务【会员、虚拟道具】、佣金、收费服务【课程】）、**内容类**（内容生成、内容互动） |
   | 自传播 Referral  |           口碑传播与病毒式传播           |                    邀请发送量、接受邀请量                    |

2. 增长引擎

   黏着式增长引擎： 关注 客户留存率、流失率、使用频率

   病毒式增长引擎： 关注 口碑

   付费式增长引擎： 关注 LTV、 CAC

## Part4：不同商业模式下的数据指标

### 电商模式

本书中的电商模式感觉像叮咚买菜、网易严选这种垂直赛道类；但是像淘宝、京东这种传统电商，他们会用大量流量吸引商家入驻平台，通过平台运营、营销活动推动线上交易。

电商的盈利模式：

- 以物还钱：自营模式（京东、天猫）、叮咚买菜、网易严选
- 佣金：天猫
- 广告：淘宝
- 增值服务：会员（88会员、京东会员）

本书提供了一种判断目前公司所处的是哪种电商阶段，即计算**年度重复购买率（年度复购率）**

- 年度复购率<40% ： 属于用户获取模式，需要重视流量获取，比如销售眼镜的电商
- 40% - 60% ：混合模式，用户获客+回头客
- more than 60% ：忠诚度模式

重要的指标：

- 转化率：衡量电商健康度
- 复购率
- GMV：包括销售额+取消订单的金额+拒收的订单金额+退货订单金额
- 购物车
- 弃买率
- 站内搜索

### 用户生成内容

内容型产品是以内容为核心卖点吸引流量从而通过各种方式变现。这里变现的各种方式包括：

- 广告：大部分内容型产品吸引了大量流量都会通过广告进行变现
- 内容付费：通过VIP会员模式解锁部分内容（知乎部分内容需要VIP才能看）、去除广告（喜马拉雅的免费音频内容前会加入广告）
- 电商模式变现：要么自建商场自营（小红书），要么抽佣变现。

内容型产品其实包括了UGC、PGC、PUGC。内容的也是多种多样：帖子、视频、音频、直播等。以UGC为例：

UGC重点关注**优质内容生成**：此内容包括帖子发布、投票、点赞、评论、不良内容举报。

重要指标：

- 用户参与度：用户回访频率、每次来访停留时间
- 内容生成：内容生成用户总量、内容生成量
- 内容互动：内容互动用户总量、评论数、点赞数、投票数、分享数
- 生成内容的价值：看到这条内容的UV、代表广告库存的页面PV
- 参与度漏斗的变化：内容平台里的用户是可以划分不同级别的，即：潜水、点赞、评论、订阅论坛、分享，不同等级代表不同程度的用户参与度和内容生成度，商业价值也不同。
- 消息提醒的有效性：在UGC商业模式中，通过**消息提醒**不断地将用户召回应用，是保持用户参与度的必备要素。

### 免费移动应用服务

移动应用服务的核心是以某个功能服务吸引用户，提升用户参与度，不断从他们身上牟利并减少他们流失。

盈利方式主要包括：

- 广告
- 付费内容：可下载内容，比如游戏里面的地图，需要花钱购买新的地图；再如马卡龙app需要购买新的模板
- 增值服务：VIP、游戏道具、功能服务（App一方面允许用户免费使用一个受限版本，一方面期望用户能够达到免费容量上限，从而开始支付费用。例如MindMaster脑图：免费版只允许增加100个节点，超过需要付费。再如Dropbox云上存储：免费版提供几个G的容量，超过既需要付费。再如processon流程图：可以存10个草稿，超过则需要付费）

移动应用服务的分析逻辑：

- CAC 与 LTV 的比较，如果LTV>CAC，则表示在用户生命周期内，能够获得的利润是高于获客成本的；同时还可计算收回CAC所需时间以及投资回报率ROI
- 应用服务类大致的流程：首先是获客得到访客，访客会使用免费版体验，不喜欢的流失，喜欢的留存；留存的用户等到试用版结束条件触发，要么流失要么付费继续使用。付费用户如果使用感不好，可能会流失。

重要指标：

- 流量获取：拉新效果、CAC。对于移动app，应用商店（Apple、安卓等）的推荐是非常重要的

- 参与度

  DAU、MAU：活跃的定义要根据自己产品的特点来画参考线。

  用户使用规律。探索用户的参与度还需要弄明白用户对产品的偏好，习惯，使用时长，不是用的功能等

- 流失率、留存率

  短期留存衡量新手流程做的是否清晰易懂；长期留存衡量产品的核心功能是否具有吸引力

  应用服务类的核心之一需要减少流失，其中精准召回就是非常总要的：发送站内信提醒

- 转化率

  试用版转化到付费版的比例

- ARPU、LTV

- 病毒性

### 双边市场

双边市场主要关注买方活动、买方活动和交易活动。其中变现主要包括：

- 佣金：滴滴、淘宝
- 广告
- 增值服务：会员、买卖双方额外服务

双边市场要解决“鸡生蛋，还是蛋生鸡的问题”，即建立双边市场的第一步是创建需求或供给能力：

- 需求（买方）：Amazon出售图书（可以现接受需求，然后延时发货）
- 供给（卖方）：Uber初期雇佣司机，创建了供给

从长期来说，需求强过供给，有了愿意花钱的人，再去找想赚钱的人是比较容易的。

重要指标：

- 买方卖方增长速率（人相关）
- 库存商品增长速率（物相关）
- 搜索相关：人均日均搜索次数、无结果返回次数/关键词、热门关键词
- 转化率：漏斗
- 交易相关
- 评价相关

## Part 5：创业阶段的划分

### 移情

找到正确的市场

### 粘性

确保额产品是具有粘性的，（用户参与度和留存）

### 病毒性

关注用户传播三种用户传播：

- 原生病毒性：相当于产品的使用功能，比如Dropbox，用户给同事或者好友分享文件时，对方可能也会使用
- 人工病毒性：物质刺激，比如Dropbox，拉新可以获得额外的空间
- 口碑病毒性：口口相传，用户自我推荐

病毒传播因子 = 邀请率 * 接受率， 注意：邀请率 = 邀请人数 / 总人数； 接受率 = 接收人数 / 邀请人数

K-factor > 1 则说明产品可以实现自给自足。

### 营收

LTV > CAC

### 规模化

关注市场

