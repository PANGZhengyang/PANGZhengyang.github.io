---
layout: post
title: "移动端设备标识码"
date: 2024-06-17
description: "设备相关基础知识"
tag: 风控
katex: true
---

IMEI（International Mobile Equipment Identity）是国际移动设备识别码的缩写。俗称“手机串号”、“手机串码”、“手机序列号”，用于在GSM（全球系统移动通信）移动网络中识别每一部独立的手机，相当于手机的身份证号码。全球每部通过正规渠道销售的GSM手机均有唯一的IMEI码。

# 安卓设备标识码

- **Device ID**

  又叫设备ID。可以用系统提供的TelephonyManager服务来获取，具有唯一性。其中又包括IMEI和MEID/ESN。一个双卡手机不止一个IMEI值，全网通双卡手机有两个IMEI和一个MEID。

  **APP重装会改变device ID**

- **IMEI**

  GSM设备返回，是联通、移动手机的标识。

  **APP重装不会改变IMEI**

- **MEID/ESN**

  CDMA手机的身份识别码，电信手机的标识，一般不使用。

- **Mac地址**

  硬件标识符，包括WiFi mac地址和蓝牙mac地址。在Android 6.0系统上，这个方法失效了，返回 “02:00:00:00:00:00” 的常量，并被判定为有害应用。另外，使用Android模拟器可以很方便的修改mac地址。

- **ANDROID_ID**

  ANDROID_ID是设备首次启动时由系统随机生成的一串64位的十六进制数字。

  设备刷机wipe数据或恢复出厂设置时ANDROID_ID值会被重置。

  在网上已有修改设备ANDROID_ID值的APP应用。

- **UDID**

  **UDID（Unique Device Identifier）设备唯一标识符。**

  **APP重装不会改变UDID**，除非root手机（普通用户无法做到）

- **UUID**

  UUID（Universally Unique Identifier）通用唯一识别码。

  **APP重装会改变UUID**

- **IDFA**

  2014年Android2.3基于Google Play推出了IDFA，功能同IOS的IDFA一样，允许用户重置或禁用该ID，由用户决定是否愿意被追踪。
  但是在中国发行的国行手机由于某些原因，google地图、Play等基础App被阉割掉了，这样导致在中国国行手机中都获取不到该IDFA。（除非用户自行Root并安装google Play）

# IOS设备表示码

- **IMEI**

  IOS 5之后被禁止
  
- **IDFA**

  IDFA（Identifier For Advertising）是一串16进制的32位串。设计目的是，标识用户设备，用于提供给各个APP之间跟踪广告所用。**同一设备下的不同app信息共享**
  
  例如，你在淘宝里搜索了某个商品之后，你在用浏览器去浏览网页的时候，那个网页的广告就会给你展示相应的那个商品的广告。
  
  IDFA有以下特点：
  
  1. IDFA和设备绑定。所有应用在统一设备上获取的都是一样的；
  
  2. iOS10之后，用户可以在设置中关闭限制APP获取；
  
  3. IDFA可以作为一台设备的唯一标识符。但是可能发生变化，如系统重置、在设置里还原广告标识符；

- **IDFV**
  
  IDFV（Identifier For Vendor）是一串16进制的32位串，**同一开发商下的不同app信息共享。**
  
  例如来自同一个开发商比如亚马逊下边的购物app和kindle app,此值是相同的。这样可以做到同一开发商下的不同app做到信息共享。
  
- **UDID**
  
  **UDID（Unique Device Identifier）这个ID是不会改变的，但是被苹果禁止**。
  
  不过可以通过uuid，写入到钥匙串中，从而获得自定义的UDID（非系统原生），即使用户重装APP，只要每次都取这个钥匙串返回，就是不变的。
  
- **UUID**
  
  UUID（Universally Unique Identifier）是国际标准化组织（ISO）提出了一个概念。它是一个32位的十六进制序列。在应用程序使用生命期间，这个 UUID保持不变。但如果用户重新安装，那么这个 UUID 就会发生变化。 **app之内信息共享**
  
  
  
  
  
  

