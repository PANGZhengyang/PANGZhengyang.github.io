---
layout: post
title: "收单与发卡"
date: 2023-08-23
description: "支付风控"
tag: 风控
katex: true
---

# 收单

“单”指消费者完成某项交易留下的单据。

“收单”指接受和处理这些这些单据。

常见的收单如下：

- ATM收单

​	持卡人在非开户行的ATM取款

- POS机

​		POS机来源于银行，这件银行即为收单行。在消费者用借记卡或者信用卡完成支付后，银行会打出一式		两份的凭据，商户保留单据是为了确保自己的利益能够通过收单行准确及时的收到。

- 网络收单

## 外卡收单

### 平台电商模式支付链

![平台电商模式支付链](\assets\risk\2023-08-23-risk-payment-acquiring-issuer\1.png)

收单（Acquiring）：对应商户侧

收款：在平台电商模式下，平台收到钱之后，需要把款项分发给“子商户”，即：资金下发。

结售汇：帮助跨境卖家把收到的外币转换成人民币结算到国内的业务。

### 独立站模式支付链

![平台电商模式支付链](\assets\risk\2023-08-23-risk-payment-acquiring-issuer\2.png)

目前大部分独立站对接的收单，主要是由支付服务商PSP、Payment Facilitator类的金融公司作为收单提供方提供收单服务（具体的收单信息流程图如下）。

![独立站模式支付链](\assets\risk\2023-08-23-risk-payment-acquiring-issuer\3.png)

## 四方模式与三方模式

### 四方模式

银行卡交易的四方模式：

- 消费者持卡人（Cardholder）
- 发卡行（Issuer）
- 收单行（Acquirer）
- 商户（Merchant）

清算机构作为连接 持卡人、发卡行、商户、收单行中间清算环节。机构包括：VISA、Mastercard

传统支付平台的价值链：支付请求会经历 **支付网关、风控、交易处理，最后才由收单行向卡组织发送支付请求**。

![四方模式](\assets\risk\2023-08-23-risk-payment-acquiring-issuer\4.png)

### 三方模式

持卡人、清算机构（既做发卡又做收单）、商户。包括：美运、JCB。这种模式清算机构拥有的数据更加齐全。

# 收单的KPI

### 支付成功率

**支付成功率（authorization rate） =  成功获得Issuer批准 / 总授权数**

常见优化支付成功率的做法有：本地收单、网络令牌化（network tokens)，动态3DS验证，智能支付路由（intelligent payment routing）等等。



**为什么本地收单能够提升支付成功率？**

首先，了解一下跨境收单与本地收单的区别。

本地收单指：采用交易发生地的本地实体；

跨境收单指：在交易发生地 **外** 的实体；

举个例子，比如一家跨境电商面向法国，且与香港一家银行进行合作成为该电商的收单行。一个法国消费者在该电商上购买物品点击支付时，香港收单行会与消费者持卡的法国发卡行联系，要求收取该笔交易款项。

但是法国发卡行并不熟悉 香港收单行，并且香港发出的收款请求信息中包含 法国发卡行 不认可的信息。

除此之外，近期 香港收单行的跨境诈骗案激增。

基于这两个理由，这笔消费被法国发卡行拒绝了，导致消费失败。

但是，如果使用本地收单，可以提升支付成功率。还是这个例子，如果这家跨境电商和法国一家收单行进行合作，则法国收单行向消费者对应的法国发卡行发送收款请求，请求信息符合本地请求，信息完备，这笔支付就能够顺利完成。

### 拒付率

**拒付率（chargeback rate） =  消费者拒绝向发卡行支付账单的比率**

拒付是海外消费者常用的一项权利，在遇到争议订单交易时消费者会向银行要求撤销已经支付的交易。常见原因有欺诈交易、货不对板、未顺利交付服务等等。


