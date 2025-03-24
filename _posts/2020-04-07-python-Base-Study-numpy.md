---
layout: post
title: "Python Base Study——Numpy"
date: 2020-04-07
description: "Python学习笔记"
tag: Python
katex: true
---

标准的Python 是C语言实现的，C中的数据类型是静态的，而Python实现的是动态数据类型。
Python整型不仅仅是一个整型，而是一个指针，指向包含这个Python对象所有信息的某个内存位置，其中包括可以转换成整型的字节。这也是为啥Python比C慢的原因之一。

而Python中的列表也是极其灵活的， 里面的元素可以使任意类型，因为列表中的每一项必须包含各自的类型信息、引用计数和其他信息，也就是说每一项都是一个完整的Python对象。

但是Python中的数组只能储存固定的类型，虽然少了些灵活性，但能有效存储和操作数据。

## 创建数组

Numpy 有很多函数可以用于创建数组

```python
np.array([1, 2, 3]) 
np.zeros() # 全0
np.ones() # 全1
np.full((2,2),3) # 全部填充3
np.eye(2) # 对角线为1
np.linspace(0,1,5) # 创建一个5个元素的数组，把5个数均匀分配到0~1
np.random.randint(1,10,(2,2)) # 2×2的[1,10)
np.random.normal(loc, scale, size) # 正态分布，默认标准正态分布
np.random.randn((2,2)) # 2×2 标准正态分布
np.random.random(size) # [0.0, 1.0)
```

## 数组索引和切片

一维数组的索引切片同`List`

二维数组的索引切片：

```python
x = np.arange(1,10).reshape(3,3)

# 索引：获取单个元素
x[1, 1] # 5

# 切片：获取子数组
x[[1], [1]] 
# array([5])

x[:2, :2] 
'''
array([[1, 2],
       [4, 5]])
'''

```

注意，数组的切片是一个**视图**而不是副本。切出来的子数组如果修改了数值，那原来的数组也会跟着改变。而对于`List`的切片则是一个副本，不会对原`list`有改变。用上面的例子继续说一下这个点

```python
x_sub = x[:2, :2] 
x_sub = np.arange(11,15).reshape(2,2)
'''
这个时候 上面的x会变成：
array([[11, 12,  3],
       [13, 14,  6],
       [ 7,  8,  9]])
'''
```

如果要创建副本用`copy`

```python
x_sub1 = x[:2, :2].copy()
x_sub1 = np.arange(11,15).reshape(2,2)
'''
x不会改变
array([[1, 2, 3],
       [4, 5, 6],
       [7, 8, 9]])
'''
```

## 通用函数

Numpy数组计算变快的关键在于利用了**向量化**操作，而**向量操作**是通过**通用函数**实现的，其目的是对Numpy数组中的值执行更快的重复操作，而避免了低效的循环。而这些通用函数主要包括：加减乘除、取模、指数，绝对值`np.abs`，三角函数`np.sin() np.cos(), np.tan()`,指数`np.exp()`,对数`np.log() , np.log2(), np.log10()`

## Axis=0 和Axis=1

`axis=0` 表示的是每一列的值都会被聚合，从而得到一行

## 数组和向量

通常情况下，Python中向量和数组之间界限比较模糊，很多时候是通用的，但是在有时候数组不一定能够表示向量，比如，利用Numpy中`random`模块中的`randn`函数生成的一维数组，既不是行向量，也不是列向量，而是秩为1的数组：

```python
np.random.randn(5) 
# array([1.76405235, 0.40015721, 0.97873798, 2.2408932 , 1.86755799])
# 注意上面只有一个中括号，它是一个秩为1的数组，通过reshape可以将其转化为向量

np.random.rand(5).shape
# (5,)

#通过reshape转换为行向量
np.random.rand(5).reshape(1, 5)
# array([[1.76405235, 0.40015721, 0.97873798, 2.2408932 , 1.86755799]])
# 注意上面变成了两个中括号了，shape为(1, 5)

#通过reshape转换为列向量
np.random.rand(5).reshape(5, 1)
'''
out:array([[1.76405235],
       [0.40015721],
       [0.97873798],
       [2.2408932 ],
       [1.86755799]])
'''
# 注意上面同样是两个中括号，shape为(5, 1)

```

## 广播

Numpy的广播机制是在不同`shape`的`ndarray`数组进行运算时，将其补足成相同`shape`再进行运算的机制。
比如我们常常需要对一个数据矩阵的每个维度进行零均值化处理，就需要该维度的所有数据减去该维度的均值：

```python
import numpy as np
# 模拟一个5*5大小的样本矩阵，每一行是一个样本的特征向量
DATA = np.random.permutation(5*5).reshape((5,5))
DATAMEAN = np.mean(DATA, axis=0)
DATA-DATAMEAN
print(DATA.shape)# (5,5)
print(DATAMEAN.shape) # (5,)
```

广播的规则：

- **规则1**：如果两个数组的维度数不相同，那么小维度数组的形状将会在**最左边**补1。
- **规则2**：如果两个数组的形状在任何一个维度上都不匹配，那么数组的形状会沿着维度为1的维度扩展以匹配另外一个数组的形状。
- **规则3**：如果两个数组的形状在任何一个维度上都不匹配并且没有一个维度等于1，那么会引发异常。

## 矩阵

向量Vector： n × 1 matrix（在计算机中 行向量是 1 × n， 列向量是 n × 1）

在`Numpy` 中使用 `× ` 矩阵乘法可能会用到广播机制；而`np.dot()`才是点乘

```python
# a matrix * a number
arr1 = np.array([1,2,3,4])
arr1 * 2

# a matrix * a vector
arr2 = np.arange(15).reshape(3,5)
vector2 = np.arange(3).reshape(3,1)

arr2 * vector2 #用到广播的机制
arr2

vector3 = np.arange(5).reshape(1,5)
vector3 * arr2 #用到广播机制

vector1 = np.arange(5).reshape(5,1)
np.dot(arr2,vector1) #矩阵相乘：m*n matrix dot multiply n*1 matrix => m*1 matrix

# a matrix multiply a matrix
arr3 = np.arange(15).reshape(5,3)
np.dot(arr2 , arr3)

# 矩阵的逆 np.linalg.inv()
arr4 = np.array([[3, 4],
                 [2,16]])
np.linalg.inv(arr4)

# 转置
arr4.T
```

矩阵不满足乘法交换律 A B 是矩阵

$$
A \times B \neq B \times A
$$

矩阵满足结合律A B C 是矩阵

$$
A \times( B \times C) = (A \times B) \times C
$$

单位矩阵(Identity Matrix) : `np.eye()` 

$$
A \times I = I \times A = A
$$

矩阵的逆(Matrix inverse) `np.linalg.inv()`：numpy 中有线性代数(linear algebra - linalg)

$$
A(A^{-1}) = A^{-1}A = I
$$

矩阵转置(Matrix transpose) `arr.T`



