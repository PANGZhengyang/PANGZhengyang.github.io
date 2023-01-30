---
layout: post
title: "Python Base Study——Module"
date: 2020-02-27
description: "Python学习笔记"
tag: Python
mermaid: true
---

This is my note for Python Module！

## 模块（Module）

在Python中，一个`.py`文件就是一个**模块**

使用模块可以提高代码的可维护性、避免函数名和变量名冲突。

## 包（Package)

为了避免模块名冲突，Python引入按目录来组织模块的方法，称为**包**

举个例子，一个`abc.py`的文件就是一个名字叫`abc`的模块，一个`xyz.py`的文件就是一个名字叫`xyz`的模块。

现在，假设我们的`abc`和`xyz`这两个模块名字与其他模块冲突了，于是我们可以通过包来组织模块，避免冲突。方法是选择一个顶层包名，比如`mytest`，按照如下目录存放：

```
mytest
├─ __init__.py
├─ abc.py
└─ xyz.py
```

引入了包以后，只要顶层的包名不与别人冲突，那所有模块都不会与别人冲突。现在，`abc.py`模块的名字就变成了`mytest.abc`，类似的，`xyz.py`的模块名变成了`mytest.xyz`。

请注意，**每一个包目录下面都会有一个`__init__.py`的文件**，这个文件是必须存在的，否则，Python就把这个目录当成普通目录，而不是一个包。`__init__.py`可以是空文件，也可以有Python代码，因为`__init__.py`本身就是一个模块，而它的模块名就是`mytest`。

## 比较好用的模块

这个部分想记录一些比较好用的模块

## pendulum

这个模块是一个时间模块，一些常见的时间操作比`datetime`更加方便；[更多内容]([Python处理时间的神器 —— Pendulum - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/420561184))

```python
# 1.更改时区
import pendulum
now_in_paris = pendulum.now('Europe/Paris')

# 2.昨天今天明天
pendulum.yesterday()
pendulum.today()
pendulum.tomorrow()

# 3.加、减
dt = pendulum.datetime(2020, 2, 27)
dt.add(years=5)
dt.subtract(months=3)

# 4.时间转字符串
dt = pendulum.datetime(2020, 2, 27)
dt.strftime('%Y-%m-%d')
dt.to_datetime_string()

# 5.字符串转pendulum
dt = '2020-01-01'
p = pendulum.parse(dt)
```

`datetime`模块里面的`timedelta`是不能直接减一年，一个月的，需要引用

```python
from dateutil.relativedelta import relativedelta
```

## Pyfiglet

这个是用来生成艺术字的模块

```python
result = pyfiglet.figlet_format("Python", font="larry3d")
print(result)
```

## Quarto

这个不是一个包，是一个展示工具，与jupyter lab配合，可将代码、markdown、图片生成html用于展示：

[点击官网](https://quarto.org/docs/get-started/hello/jupyter.html)

先下载安装Quarto，打开jupyter lab可以使用terminal来写quarto语句生成html。

如果打不开terminal可以[点击该篇文章](https://blog.csdn.net/qq_39567427/article/details/113520095)

## pysnooper

将代码每一步打印出来，做Debug。

```python
# 装饰器写法：
@pysnooper.snoop()
def foo():
    a= []
    for i in list(range(3)):
        a.append(i+1)
    return a
foo()

# 上下文写法：
a = []
with pysnooper.snoop():
    for i in list(range(3)):
        a.append(i+1)
    
```

## langid

可以识别文字是什么语言

```python
import langid
s1 = '你好世界，好好学习啊'
langid.classfy(s1)
```

返回两个字母的ISO：[请参考这篇](https://baike.baidu.com/item/ISO%20639-1)