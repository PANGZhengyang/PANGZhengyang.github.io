---
layout: post
title: "Token Payment"
date: 2025-04-15
description: "支付风控"
tag: 风控
katex: true
---

# Token Payment

Token：加密后的银行卡号（PAN，Primary Account Number）

Token要素

- Token Expiry Date：token的有效期，一般是3-6位的数字，在EMVco中约定按照ISO 8583规定，4位数字，比如YYMM，也就是年月，跟银行卡一样，选择到期年月。

- Token Assurance：token的担保级别，用于表示该token的可信度，一般用00-99的两位数字表示，受身份识别认证以及风险评分等因素影响，在认证通过之后生成。

- Token Domain：token的使用场景，也称为域控，比如支付渠道，不同的使用场景其风险高低不一样。一般域控的变更需要重新再申请TRID。

- Token Location：指token存储的所在地，比如存储在商户服务器或者手机芯片存储（SE）等。

- Payment Account Reference：简称PAR，直译就是支付账户参考编号，用于PAN表示的支付帐户与对应的token进行关联，不可以直接用于支付。

  另外跟token关联的要素还包括使用的次数和交易限额等，主要用于控制交易风险。

- ID&V：全称是Identification and Verification，也就是基于风险的持卡人的身份识别和认证
- De-Tokenisation：解token或者去标记化，主要用于在支付环节，因为token化之后，真实银行卡信息都变成了token号等信息，但是实际发卡行在支付交易中仍只能验证真实的银行卡信息，所以需要通过解token获得对应的卡信息，再上送发卡行完成支付交易。

**Token Payment是指为防止银行卡信息直接泄露，先将银行卡token化，在支付过程中，通过上送token信息而不是银行卡信息来实现支付。**

# 产品机制

TR（Token Requestor）指申请token的一方，比如商户或者收单机构。

TSP（Token Service Provider）指token的发行机构，TR通过系统与TSP对接，申请token并在支付交易时，通过调TSP的系统完成解token然后再完成支付，同时TSP还会负责token的标准建立、token库的维护及token的日常管理等。“四方模式”下TSP的角色一般由卡组织来扮演。

Payment System：支付交易系统，这里通常包括收单机构、卡组织和发卡行共同组成的支付交易系统，就是在通过TSP解token（De-Tokenisation ）后使用原卡数据完成剩余支付流程的相关系统。

## Token 申请

（1）成为TR：向TSP申请；

（2）基于PAN申请token：通常的方式就是上送银行卡的信息，比如卡号、有效期以及CVV等信息，待TSP通过卡组自身风控系统及发卡行的ID&V认证后，TSP即会返回token信息给到TR。

```
现在打开了一款购物APP，准备绑定一张银联信用卡。在绑卡页面，填写完卡片基本信息，点击“提交”。

这时候，APP作为TR，会向银联（TSP）发起一个Token申请。

银联收到申请后，根据APP采集来的信息与 发卡行 进行验证。

账户验证无误，银联向APP下发一个与你的银联卡相对应的Token号，仅限于该APP内支付使用。
```

## Token 使用

（1）用户在确认支付后，TR就带上token信息（包括TRID）调TSP发起token payment请求。

（2）TSP收到token payment请求后，校验通过将进行detoken，获取初始的卡信息（包括卡号、有效期、CVN等），并组装卡信息和其他信息成授权请求报文调支付交易系统发起授权请求。

（3）发卡行进行支付信息校验（ID&V），校验成功，原路返回授权结果，并最终通知持卡人支付成功。

![Token使用](\assets\reading minutes\2025-04-15-risk-payment-token\1.png)

