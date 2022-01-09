---
layout: post
title: "Python Base Study——Fundamention"
date: 2020-02-01
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

## 迭代器

- 迭代：遍历

- 可迭代对象：`List, string, tuple, set`

  如何判别一个对象是否是可迭代对象

  ```python
  from collections.abc import Iterable
  
  isinstance([], Iterable)
  #True
  ```
  
- 迭代器：带有`next()`方法的可迭代对象
  
  如何判别一个对象是否是可迭器
  
  ```python
  from collections.abc import Iterator
  
  isinstance([], Iterator)
  # False
  ```
  
  通过`iter()`可以将可迭代对象变成迭代器
  
  ```python
  lst = [1,2,3]
  isinstance(iter(lst), Iterator)
  # True    
  ```
  
  Python 中的`for`循环就是先通过函数`iter()`先获得一个迭代器，然后再不断调用`next()`函数实现的：
  
  ```python
  for x in [1, 2, 3]:
      print(x)
      
  # 等价于
  it = iter([1, 2, 3])
  while True:
      try:
          x = next(it)
          print(x)
      except StopIteration:
          break
  ```
  
## 生成器

生成器也是迭代器。生成器的优点就是可以节约存储空间，我们不需要一次性把这个列表创建出来，只需要**记住它的建立规则，之后需要使用的时候一遍计算一遍创建** 。[更多例子](https://mp.weixin.qq.com/s/mKTRn3r5WnPWQi84AQH6Ag)

  含有`yield`关键字的函数就是生成器，执行过程比较特别： 

  - 调用该函数的时候不会立即执行代码，而是返回了一个生成器对象；
  -  当使用 `next()` (在 `for` 循环中会自动调用 `next()` ) 作用于返回的生成器对象时，函数 开始执行，在遇到 `yield` 的时候会**暂停**，并返回当前的迭代值；
  -  当再次使用 `next()` 的时候，函数会从原来**暂停**的地方继续执行，直到遇到 `yield` 语 句，如果没有 `yield` 语句，则抛出异常；

## 上下文管理器

实现了`__enter__`和`__exit__`方法的对象满足上下文管理器

通常，我们使用`with`语句调用上下文管理器。`with` 语句尤其适用于对资源进行访问的场景，确保执行过程中出现异常情况时也可以对资源进行回收，比如自动关闭文件等。 

`__enter__` 方法在 `with` 语句体执行前调用，`with` 语句将该方法的返回值赋给 `as` 字句中 的变量，如果有 `as` 字句的话。 

`__exit__ `方法在退出运行时上下文 时被调用，它负责执行**清理**工作，比如关闭文件，释放资源等。如果退出时没有发生异常，则 `__exit__` 的三个参数，即 `type`, `value` 和 `traceback` 都为 `None`。如果发生异常，返回 `True` 表示不处理异常，否则会在退出该方法后重新抛出异常以由 `with` 语句之外的代码逻辑进行处理。

## 进程与线程

进程(Process) 就是一个任务，比如打开一个Word就是一个进程，打开一个Chrome就是一个进程。单核计算机处理进程，类似给进程1 分配0.01s时间，进程2分配0.01s时间，而不是等一个进程结束，在进行第二个进程。

线程（Thread）是一个进程里的一个子任务。比如Word里一边打字，一边可以检查错误拼写，这就是两个线程。在举个列子，播放器播放视频，一个线程播放画面，一个线程播放音频。

一个进程有一个或多个线程，多个进程互不影响，但是一个进程里的多个线程中的其中一个线程崩了，会导致其他线程和进程崩掉。一个进程是线程的容器。

## 标准模块

### collections

collections包含5个高性能的数据类型：

- `Counter`: 计数器
- `OrderedDict`：有顺序的字典
- `defaultdict`：有默认值的字典
- `namedtuple`：生成可以通过属性访问元素内容的 tuple 子类
- `deque`：双端队列，能够在队列两端添加或者删除元素

```python
from collections import Counter

s1 = 'asdfghjkasdfgsdf'
c = Counter(s1)
c 
# Counter({'s': 3, 'd': 3, 'f': 3, 'a': 2, 'g': 2, 'h': 1, 'j': 1, 'k': 1})

from collections import defaultdict
d = dict([('a', 10), ('b', 20), ('d',23), ('c', 15)])
default_d = defaultdict(lambda: 9999)
default_d.update(d)
# defaultdict(<function __main__.<lambda>()>,
#           {'a': 10, 'b': 20, 'd': 23, 'c': 15})
default_d['z'] 
# 9999

from collections import deque
q = deque([1,2,3,4,'a'])
q.append('s') # deque([1,2,3,4,'a','s'])
q.appendleft('d') # deque(['d',1,2,3,4,'a','s'])
q.rotate() #翻转
```

### datetime

```python
from datetime import datetime

'''
1. datetime 包括年、月、日 小时、分钟、秒 微秒
t = datetime.now()
t.year t.month t.day t.hour t.minute t.second

2. 两个datetime 如t1，t2 相减会得到一个 timedelta
(t2 - t1).seconds 会得到相减之后,用seconds 可以得到秒数

3. strftime - 将datetime转变为string格式
   datetime.strptime('2021-11-11','%Y-%m-%d') - 将string转变为datetime
'''
```

### hashlib

```python
import hashlib

'''
hashlib 提供的是摘要算法：通过一个函数，把任意长度的数据转换为一个长度固定的数据串（通常用16进制的字符串表示）
摘要函数是一个单向函数，计算 f(data) 很容易，但通过 digest 反推data会比较难
同一个data使用同一个摘要函数得出的结果是相同的

.hexdigest(): 16进制str类型的消息摘要
.digest(): bytes类型的消息摘要
'''

# MD5(Message-Digest Algorithm 5):生成结果是固定的16字节，通常用一个32位的16进制字符串表示
md = hashlib.md5()
md.update('test'.encode('utf-8'))
md.hexdigest() # '098f6bcd4621d373cade4e832627b4f6'
md.digest() # b"\t\x8fk\xcdF!\xd3s\xca\xdeN\x83&'\xb4\xf6"

# SHA(Secure Hash Algorithm):生成的结果是20字节长度，通常用一个40位的16进制字符串表示
hashlib.sha256('test'.encode('utf-8')).digest().hex()
# '9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08'


```

