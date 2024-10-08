---
layout: post
title: "SWIFT"
date: 2024-09-27
description: "支付风控"
tag: 风控
katex: true
---

# SWIFT

SWIFT全称是环球银行金融电信协会（Society for Worldwide Interbank Financial Telecommunication）**主要职能是在全球银行系统之间传递结算信息。**

注意：**本身SWIFT只是金融机构间的一个通讯工具**，**并不参与清算和资金转移的过程**，简单来说就是传递信息流；而资金流的划拨则有代理行、中转行、当地清算机构等来完成，比如：人民银行大额支付系统、中国CIPS、美国CHIPS。

理论上所有的跨境转账都需要经过SWIFT。

**场景1：汇出行和收款行在同一国家，银行间有合作关系，货币无需转换。**

**报文类型有哪些？**实际上MT类型有**几种业务场景**：Customer Transfer（客户汇款，MT1XX类型）、Financial Institution Transfers（银行头寸调拨，MT2XX类型）、Foreign Exchange（外汇买卖，MT3XX类型）、Collections+Cash Letters（托收，MT4XX类型）、Securities证券、Precious Metals and Syndication（贵金属和银团贷款）、Documentary Credits and Guarantees（跟单信用证和担保，MT7XX）、Traveler’s Cheque（旅行支票）、Bank Statement（银行对账，MT9XX类型）。

![场景1](\assets\risk\2024-09-27-risk-payment-swift\1.png)

这种模式最简单，收款和汇出行都在同一清算网络，直接清算就可以，无任何难度。

**场景2：汇出行和收款行在不同国家且汇款行没有参与B国清算网络，收款行在A国有代理行，货币需要转换**

![场景2](\assets\risk\2024-09-27-risk-payment-swift\2.png)

这种场景假设收款行在A国有个分行，可以作为代理行收款。这样汇款行和代理行可以通过A国当地清算网络完成清算，然后代理行再和收款行通过清算网络B结算，当然因为都是一家人，不结算也无所谓。货币转换可以由汇款行进行，也可以在其他银行换汇。

**场景3：汇出行和收款行在不同国家，A国和B国无金融合作关系，各自在对方国家没有代理行，且货币需转换。**

![场景2](\assets\risk\2024-09-27-risk-payment-swift\3.png)

这种情况比较复杂，A国和B国甚至是敌对关系，双方都在对方国家没有代理行想要把钱汇过去怎么办呢？只好找第三国的银行做中间行，中间行在两国都有代理行或者分支机构。这样资金通过2-3个清算网络异常曲折的到达了收款人手中，所以有些时候到手的汇款金额少了很多，就是这种情况下代理行和中间行收取了大量的汇款手续费以及换汇加点等所导致的。

# CIPS

人民币跨境支付系统（Cross-border Interbank Payment System，简称CIPS）是中国人民银行专门为跨境人民币支付业务开发的资金清算结算系统。它旨在向境内外参与者的跨境人民币业务提供资金清算、结算服务，为人民币国际化铺设“高速公路”。CIPS主要处理人民币与其他货币之间的转账和支付事务，其功能更专注于跨境支付和清算。

CIPS负责人民币（在岸离岸转换）在岸转离岸人民币的信息流还是会走Swift



