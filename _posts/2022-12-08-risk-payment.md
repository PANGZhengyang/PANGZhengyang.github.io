---
layout: post
title: "支付风控学习笔记"
date: 2022-12-07
description: "支付风控"
tag: 风控
katex: true
---

最近在了解支付风控，对此做了笔记。

# 支付风险类型

1. 账户风险：黑客盗取账户。拖库（对目标数据库进行攻击）、洗库（对账户进行破解）、撞库（尝试登陆）

2. 交易风险：交易过程中的恶意行为，主要分为欺诈和刷单。

   - 欺诈风险：盗用别人的身份进行交易。解决方法：找到交易的异常行为：异地、超额交易等加入增强验证；

   - 恶意刷单：商家为了获取补贴曝光从而恶意刷单。解决方法：识别用户多次同店交易，或者用户关联关系防止刷单风险。

     刷单特征：小号刷单、使用虚拟机、使用VPN、刷虚拟物品、交易的商品种类少；

   3. 合规风险：当地的法律、政策等。
   4. 洗钱风险：将黑钱通过第三方平台，比如在游戏里购买虚拟资产等。

# 支付风控的流程

支付风控的核心为实时交易进行风险评估，对欺诈进行检测。需要注意时效性（用户体验）和有效性（风险）。

主流的风险等级：

- 三等级：低、中、高风险。大部分交易是低风险的，不需要拦截直接放行。中风险的交易是需要进行增强验证，确认是本人操作后放行。高风险的交易则直接拦截。
- 四等级：在之前的基础上会新增一个中高风险等级。此类交易在用户完成增强验证之后还会加入管理人员核实，核实没问题后交易才能放行；
- 五等级：在四等级的基础上，会增加一个中低风险等级。此类交易是先放行，但是管理人员需要进行事后的核实。如果核实有问题，通过人工方式执行退款，或者提升该用户的风险等级。

# 基于规则的风控

- 名单规则

  使用白名单或者黑名单来设置规则。包括用户ID、IP地址、设备ID、地区、公检法协查等。比如：

  1. 用户ID是在风控黑名单中。
  2. 用户身份证号在反洗钱黑名单中。
  3. 用户身份证号在公检法协查名单中。
  4. 用户所使用的手机号在羊毛号名单列表中。
  5. 转账用户所在地区是联合国反洗钱风险警示地区。

- 操作规则

  对支付、提现、充值的频率按照用户账号、IP、设备等进行限制，一旦超出阈值，则提升风控等级。

  1. 频率需综合考虑(五)分钟、(一)小时、(一)天、(一)周等维度的数据。由于一般计算频率是按照自然时间段来进行的，所以如果用户的操作是跨时间段的，则会出现频率限制失效的情况。当然，比较复杂的可以用滑窗来做。
  2. 对不同的风险等级设置不同的阈值。
  3. 用户提现频次5分钟不能超过2次， 一小时不能超过5次，一天不能超过10次。
  4. 用户提现额度一天不能超过1万。
  5. 用户支付频次5分钟不能超过2次，一小时不能超过10次，一天不能超过100次。

- 业务规则

  和特定各业务相关的一些规则，比如：

  1. 同一个人绑定银行卡张数超过10张。
  2. 同一张银行卡被超过5个人绑定。
  3. 同一个手机号被5个人绑定。
  4. 一个周内手机号变更超过4次。
  5. 同一个对私银行卡接受转账次数一分钟超过5次。

- 行为异常

  用户行为和以前的表现不一致，比如：

  1. 用户支付地点与常用登录地点不一致
  2. 用户支付使用个IP与常用IP地址不一致
  3. 用户在短时间内，上一次支付的地址和本次支付的地址距离非常远。比如2分钟前在中国支付的，2分钟后跑到美国去支付了

支付风控的规则可参考几个维度：

账户、设备、位置、行为、关系、偏好

1. 账户
   - 近期注册，有大量转账记录，且为收款和提现；
   - 两个账户之前从未有直接转账；
2. 设备
   - 陌生设备；
3. 位置
   - 经常交易地点，是否异地，两地的联系；
4. 行为
   - 交易时间：非正常营业时间（23：00 - 次日9：00）尽量不要出现大额交易；
   - 交易金额：金额大小、单笔交易金额不要是整数，数字不一样；
   - 交易频次：30min内不要连续刷3笔，两笔交易时间间隔大于2min；
5. 关系
   - 转入转出是否有人际关联；
   - 转入账户是否有过不良记录、黑名单记录
6. 偏好
   - 指纹支付到密码支付；
   - 日常小额度忽然变成大额度；
   - 传感器技术：指压、接触面积、重力变化、连续间隔时间来判断是不是本人（每个人以不同的方式来触摸手机）；

可采取的措施（增强验证）：

1. 活体识别、真人检测（人脸、指纹）；
2. 风险提示；
3. 填写转账人的姓名；
4. 换设备时，使用原手机、找同事或通讯录验证本人；
5. 拦截；
