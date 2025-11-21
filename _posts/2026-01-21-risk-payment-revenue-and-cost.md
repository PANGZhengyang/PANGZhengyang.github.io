---
layout: post
title: "跨境支付的收益和成本"
date: 2026-01-21
description: "支付风控"
tag: 风控
katex: true
---



一笔跨境支付发生，商户端一般要承担一系列费用，这个费用称为MDR（Merchant Discount Rate）：

- Interchange Fee（发卡行分成，交换费）：占总收入的1%-2%，直接服务客户，所以占大头
- 卡组费用：占总收入的0.1%-0.4%
- 收单行服务费：占总收入的0.1%-0.4%



# Interchange费率

平均而言，Interchange 费在欧洲约为交易金额的 0.3-0.4%，在美国为 2%。

- 金额由银行卡组（Visa、Mastercard、银联等等）决定，且决定后不可协商。

- 费率也会定期调整。例如，Visa 和万事达便会在每年 4 月和 10 月公布新的费率。

查看银行卡组织官网，便可找到当前最新最准确的费率。

Interchange费受许多因素影响：

- 银行卡组织
- 有卡交易与无卡交易：有卡交易Interchange低（风险低）
- MCC：慈善机构、旅行社、公用事业等机构IC低
- 交易地区：本地交易IC低

# 银行卡交易定价模式

包括IC++与混合定价（Blended Pricing）。两者区别在于费用透明度。

IC++明确成本明细为Interchange费（发卡银行收取的费用）+ Card Scheme费（银行卡组织收取的费用）+ Acquirer markup （收单行收取的费用）；

Blending Pricing则由收单机构或支付服务商提供一个统一的费率，商户不必关心背后的复杂规则，只需按固定比例支付费用。

