---
layout: post
title: "Python Base Study——Fundamention"
date: 2020-08-23
description: "Python学习笔记"
tag: Python
mermaid: true
---

This is my note for Python (including data structure, logical language and so forth). When I face some tricky problems, I'll record them.
Let's start!

## 编码

<div class="mermaid">
graph LR
    id1(ASCII-美国)
    id2(GBK-中国)
    id3(Shift_JIS-日本)
    id4(统一为Unicode)
    id5(考虑到节约资源问题：UTF-8)
    id1 --> id4 
    id2 --> id4 
    id3 --> id4
    id4 --> id5
</div>

## 切片

```python
#想取最后3个数字：如果希望切片包含最后一个元素，可将第 2 个索引置为空
numbers = [1, 2, 3, 4, 5, 6]
numbers[-3:]

#当使用负步长时，要确保左边索引大于右边索引，注意左边能取，右边不能取
numbers = [1, 2, 3, 4, 5, 6, 7, 8]
numbers[4:0:-1]
```

## 拷贝

```python
d1 = {'name': 'ethan', 'books': ['book1', 'book2', 'book3']}
d2 = d1 #copy的是同一个对象
d2['books'].remove('book3')
#d1 会发生改变
d1

d1 = {'name': 'ethan', 'books': ['book1', 'book2', 'book3']}
d2 = d1.copy() #浅拷贝：对不可变的对象修改保持独立，对可变对象的修改保持同步
#name是不可变的因为string是一个不可变对象，而books是可变的对象因为list是一个可变对象
d2['name'] = 'pzy'
#d1是不发生改变的
d1 

d1 = {'name': 'ethan', 'books': ['book1', 'book2', 'book3']}
d2 = d1.copy()
#改变的是list中的元素所以d1也会跟着改变
d2['books'].remove('book3')
d1

from copy import deepcopy
d1 = {'name': 'ethan', 'books': ['book1', 'book2', 'book3']}
d2 = deepcopy(d1) #深copy：对可变对象的修改保持独立
d2['books'].remove('book3')
#d1不会改变
d1
```

## 函数中的参数

- 必选参数

- 默认参数：必须写在必须参数后面，且应该使用不可变对象

- 可变参数：`*args` 传递不定数量的参数

  ```py
  def f(*num):
  	print(num)
  ```

  在函数内部，参数num接受到的是一个`tuple`

  `*`还可以用来传参

  ```python
  def add(x,y,z):
      return x+y+z
  
  
  a = [1,2,3]
  add(*a) #6
  ```

- 关键字参数：`**kwargs`传递不定长度的键值对参数

  ```python
  def f(**kwargs):
      return(kwargs)
  
  
  f()
  # {}
  
  f(x=1)
  # {x:1}
  ```

  在函数内部，kwargs接受的是一个`dict`

  同样地`**`也可以用来传参
  
  ```python
  x = {'a':1}
  f(**x)
  #{'a':1}
  ```

  - 组合参数  顺序：必选参数-默认参数-可变参数-关键字参数
  
    ```python
    def func(x, y, z=0, *args, **kwargs):
        print ('x =', x)
        print ('y =', y)
        print ('z =', z)
        print ('args =', args)
        print ('kwargs =', kwargs)
    
        
    a = [1,2,3,4]
    b = {'pzy':'yes'}
    func(*a,**b)
    ```
    
    ```
    x = 1
    y = 2
    z = 3
    args = (4,)
    kwargs = {'pzy': 'yes'}
    ```
    
    ```python
    func(1,2,3,4,5,6,a=1,b=2)
    ```
    
    ```
    x = 1
    y = 2
    z = 3
    args = (4, 5, 6)
    kwargs = {'a': 1, 'b': 2}
    ```
## 高阶函数（Higher-order Functions)

一个函数接受另一个函数作为参数。

- map(function, sequence)

- filter(function, sequence) ：从`sequence`中筛选出符合`function`的

- reduce(function, sequence[, initial])

  ```python
  from functools import reduce
  
  reduece(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
  ```

## 闭包

内函数使用了外函数的自由变量，当外函数的生命周期结束了，内函数任然可以使用之前的自由变量。

闭包的优点就是可以节省资源。

```python
from math import pow

def make_pow(n):
    def inner_func(x): # 嵌套定义了 inner_func
        return pow(x, n) # 注意这里引用了外部函数的 n
    return inner_func # 返回 inner_func


pow2 = make_pow(2)
pow2(5)

del make_pow
pow2(6)
```

## 装饰器

在不改变主函数的代码下，用另一个函数去增加主函数的功能。

```python
from functools import wraps

def decorator(func):
    
    @warps(func) #改变地址
    def wrapper(*args,**kwargs):
        ...
        func(*args,**kwrags)
        ...
        return ...
    return wrapper

def func1(x):
    return ...

func1 = decorator(func1)
func1(a value)
#等价于使用语法糖
@decorator
func1(a value)
```

当有多个装饰器时，靠近主函数的优先执行。



