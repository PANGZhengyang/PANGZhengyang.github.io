---
layout: post
title: "浅入浅出支付"
date: 2023-06-18
description: "支付风控"
tag: 风控
katex: true
---

# 何为支付

“支付”这个词想必大家都比较熟悉，我们去小店铺买商品，在淘宝上买衣服都会发生支付付款的行为，但是大家也只是参与到了部分”支付“环节。广义上来说，支付包括了交易、清分和结算。

## 清分和结算

关于清分、结算有几种不同的理解，暂且先用最简单的一种理解方式：清算=清分+结算。清分是算清楚各方的费用；结算是跨行转账。

关于三者的系统实现，需要引入三类机构，银行、清算机构、人行。

银行主要是做自己内部的账务管理。清算机构对跨行交易进行清分、清算，目前常见的清算机构包括银联、网联等。最后通过人行的大小额支付系统等，完成最后的结算。

但是在陈斌的《一本书读懂支付》中，结算是PSP与商户之间算清一个结算周期内的支付款项的过程。而清算是机构（银行/金融机构）之间算清相互之间欠款项的过程。

## 二清

非持牌机构，在一笔交易中做了一次倒手，先把付款方的钱放到自己的账户里，再从自己的账户把钱打给收款方。在OTA场景中，二清可以描述为以下流程：

![二清](\assets\risk\2023-06-18-risk-payment-fundamental\1.png)

二清存在以下问题：

- 如果二清机构跑路，那么商户的钱就拿不到；
- 客户的钱到二清机构，信息流就中断了，资金来源和走向不能被监管机构统计；

解决二清：

- 收购有支付牌照的公司
- 接入分账服务商（见证宝：平安银行提供的服务，将从客户处收到的钱放置在平安银行见证宝的账户中）

# 支付服务提供商

支付的类型是多种多样的，每个银行都有自己的POS机，对于一个商户来说，客户A如果使用的是工行，客户B使用的是招行，那么商户需要两个POS机（工行、招行），让一个普通的商户去适应多种不同类型的支付方式是一件非常困难的事情。为了解决这个问题，市场上出现了能够聚合各种不同的支付方式的，为商户提供统一解决方案的支付服务提供商（Payment Service Provider，PSP）

## 第三方支付

PSP的一种，处于付款方（第一方）和收款方（第二方）之间。

例如wechatPay，Alipay均为第三方支付。本质的逻辑就是：如果不使用第三方支付，商户就得去接各种支付方式，可能存在多个POS机（VISA/Mastercard/UnionPay/美运等等）。有了三方支付公司（会把这些都整合在一起），商户可以更专心的在自己的业务中。

## 聚合支付

第三方公司也不可能将所有支付方式穷尽，这个时候专门整合第三方支付公司业务的聚合支付公司应运而生。

# 备付金

从商户角度：在约定的结算周期内，支付机构即将结算给商户的待付资金。

例如：某OTA平台有自己的支付公司，明天要将100w划拨给商户，100w为备付金。

# 卡基与账基

卡基是以卡片作为支付方式。通过媒介提供并验证卡信息进行支付的行为，媒介包括POS、闪付、电话支付、线上收银台等载体。

卡基核心是卡号，资产存储在卡号上；支付媒介不仅有刷卡，还包括POS、闪付、电话支付、网银支付、线上无磁无密支付等通过卡信息进行支付的载体。



账基是以账户为支付工具，自动发卡程序提供并验证账户信息进行支付的行为，账户包括银行账户、钱包账户等账户种类。

账基核心是实名认证+密码验证（秘钥、数字、指纹等）资产存在账户里。既可以使用余额，也可以使用银行卡等各种资产，常见的有微信支付、支付宝。

# 授权、预授权、请款、代扣

**授权**指的是像银行或信用卡公司这样的发卡机构，对消费者的付款请求进行验证的过程。审核通过后，发卡行在持卡人账户上预留授权金额，并做好实际资金转移的准备。授权率是指成功授权的交易所占的百分比。

**预授权**指商户在持卡人消费前先冻结一部分资金，在消费完以后持卡人签字，商户才能正式扣掉这部分资金。信用卡预授权也可以理解为押金，当消费者持信用卡订酒店或订机票时，银行会先冻结信用卡里一部分资金，作为商户消费的押金，在完成消费后，经持卡人签字确认即预授权确认，商户才能正式扣掉这部分资金。

**请款**指在交易授权后将准备金从购物者转移到商户。

**代扣**指个人和平台签订协议给平台授权，授权后定期从银行卡中扣除费用。

**代付**指企业向个人打钱。
