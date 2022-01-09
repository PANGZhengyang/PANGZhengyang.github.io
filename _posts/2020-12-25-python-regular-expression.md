---
layout: post
title: "正则表达式"
date: 2020-12-25
description: "正则表达式"
tag: Python
---

# 正则表达式

 ﻿# 一、正则表达式基本知识
## 1.操作符
![在这里插入图片描述](https://img-blog.csdnimg.cn/f3eee02716e640c09dbf1151d26345fa.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)
## 2.量化符
![在这里插入图片描述](https://img-blog.csdnimg.cn/33809cc0b6da4affa473bbd86fc4be06.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)
## 3.特殊符号
![在这里插入图片描述](https://img-blog.csdnimg.cn/35e3c4141e184ef1aba69ed5aa7816d7.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_18,color_FFFFFF,t_70,g_se,x_16#pic_center)
## 4.修饰符 flag
re.I -- 使匹配对大小写不敏感
re.S -- 使 . 匹配包括换行在内的所有字符

## 5.常用方法
1）re.match(pattern, string ,flag) -- 匹配字符串的**开头**，如果开头匹配不上，则返回None；用得不多
2) re.search(pattern, string ,flag)  --扫描整个字符串，匹配后立即返回，不在往后面匹配；
3）re.findall(pattern, string ,flag) --扫描整个字符串，以**列表**形式返回**所有**的匹配值
4)compile()：将字符串编译成正则表达式对象，供 match() 、 search() 和findall()函数使用；
5)sub()：扫描整个字符串，用于替换字符串的某些值；
6)split()：扫描整个字符串，按照指定分隔符切分字符串；

其他复杂的操作请参考：[正则表达式](https://blog.csdn.net/weixin_40907382/article/details/79654372)
