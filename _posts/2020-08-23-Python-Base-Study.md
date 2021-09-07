---
layout: post
title: "Python Base Study"
date: 2020-08-23
description: "Python学习笔记"
tag: Python
---

This is my note for Python (including pandas, numpy, matplotlib and so forth). When I face some tricky problems, I'll record them.
Let's start!

# 1.Rename: 更改名字
```python
df.rename(index ={'a':1},inplace = True)
df.rename(columns = {'guiyang':2},inplace = True)

#如果只是改index的名字的话
df.index.name = xxx
```
# 2.Hierarchical Index: 多层索引
##  2.1多层索引的创建


```python
#method 1: pd.MultiIndex.from_product() --不常见
import pandas as pd
import numpy as np

data = np.random.randint(0,100,size=(6,3))
names = ['张三','李四','王五']
exam = ['期中','期末']
index = pd.MultiIndex.from_product([names,exam])
columns = ['Jave','Web','Python']

df = pd.DataFrame(data=data,index=index,columns=columns)
df

#method 2: 使用set_index(['name','examl']);当然格式和上面的方法不一样，只是set_index可以构建多层索引
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Jave</th>
      <th>Web</th>
      <th>Python</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="2" valign="top">张三</th>
      <th>期中</th>
      <td>20</td>
      <td>77</td>
      <td>23</td>
    </tr>
    <tr>
      <th>期末</th>
      <td>18</td>
      <td>33</td>
      <td>29</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">李四</th>
      <th>期中</th>
      <td>43</td>
      <td>68</td>
      <td>59</td>
    </tr>
    <tr>
      <th>期末</th>
      <td>54</td>
      <td>47</td>
      <td>43</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">王五</th>
      <th>期中</th>
      <td>17</td>
      <td>62</td>
      <td>79</td>
    </tr>
    <tr>
      <th>期末</th>
      <td>80</td>
      <td>47</td>
      <td>11</td>
    </tr>
  </tbody>
</table>

## 2.2多层索引的取值

```python
#使用 .loc[]
df.loc['张三','期末']
```

    Jave      18
    Web       33
    Python    29
    Name: (张三, 期末), dtype: int32



## 2.3多层索引的排序


```python
# 使用sort_index 问了展示的更好 用下面的例子
import pandas as pd
import numpy as np 

data = np.random.randint(0,100,size=(9,3))
key1 = ['b','c','a']
key2 = [2,1,3]
index = pd.MultiIndex.from_product([key1,key2])
df = pd.DataFrame(data=data,index=index,columns=['Java','Web','Python'])
df
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Java</th>
      <th>Web</th>
      <th>Python</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th rowspan="3" valign="top">b</th>
      <th>2</th>
      <td>3</td>
      <td>66</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>36</td>
      <td>3</td>
      <td>24</td>
    </tr>
    <tr>
      <th>3</th>
      <td>18</td>
      <td>79</td>
      <td>21</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">c</th>
      <th>2</th>
      <td>93</td>
      <td>47</td>
      <td>37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>20</td>
      <td>44</td>
      <td>81</td>
    </tr>
    <tr>
      <th>3</th>
      <td>12</td>
      <td>78</td>
      <td>41</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">a</th>
      <th>2</th>
      <td>62</td>
      <td>92</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>37</td>
      <td>1</td>
      <td>41</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>18</td>
      <td>94</td>
    </tr>
  </tbody>
</table>



```python
#level = 0从外层开始; 如果level=1， 从第二层开始
print(df.sort_index())
print('-----------------------')
print(df.sort_index(level=1))
```

         Java  Web  Python
    a 1    37    1      41
      2    62   92      10
      3     2   18      94
    b 1    36    3      24
      2     3   66      38
      3    18   79      21
    c 1    20   44      81
      2    93   47      37
      3    12   78      41
    -----------------------
         Java  Web  Python
    a 1    37    1      41
    b 1    36    3      24
    c 1    20   44      81
    a 2    62   92      10
    b 2     3   66      38
    c 2    93   47      37
    a 3     2   18      94
    b 3    18   79      21
    c 3    12   78      41


## 2.4set_index() 与 reset_index()
1.set_index('country',drop = True) 将‘country’这一列作为index ,drop = True 删除用作新索引的列（也就是把之前的‘country'列删了）! **宽表变为长表**

2.reset_index(drop= True)  第一种是对原来的数据表的index进行reset： 如果这个表有数据删除 则用reset_index重排一下index ,drop = True的话 要删除之前的index!


```python
df.reset_index(drop = True)#drop = True的话 要删除之前的index!
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Java</th>
      <th>Web</th>
      <th>Python</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>66</td>
      <td>38</td>
    </tr>
    <tr>
      <th>1</th>
      <td>36</td>
      <td>3</td>
      <td>24</td>
    </tr>
    <tr>
      <th>2</th>
      <td>18</td>
      <td>79</td>
      <td>21</td>
    </tr>
    <tr>
      <th>3</th>
      <td>93</td>
      <td>47</td>
      <td>37</td>
    </tr>
    <tr>
      <th>4</th>
      <td>20</td>
      <td>44</td>
      <td>81</td>
    </tr>
    <tr>
      <th>5</th>
      <td>12</td>
      <td>78</td>
      <td>41</td>
    </tr>
    <tr>
      <th>6</th>
      <td>62</td>
      <td>92</td>
      <td>10</td>
    </tr>
    <tr>
      <th>7</th>
      <td>37</td>
      <td>1</td>
      <td>41</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2</td>
      <td>18</td>
      <td>94</td>
    </tr>
  </tbody>
</table>



```python
df.reset_index(inplace= True)
df.set_index(['Python'],drop = True)
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>level_0</th>
      <th>level_1</th>
      <th>Java</th>
      <th>Web</th>
    </tr>
    <tr>
      <th>Python</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>38</th>
      <td>b</td>
      <td>2</td>
      <td>3</td>
      <td>66</td>
    </tr>
    <tr>
      <th>24</th>
      <td>b</td>
      <td>1</td>
      <td>36</td>
      <td>3</td>
    </tr>
    <tr>
      <th>21</th>
      <td>b</td>
      <td>3</td>
      <td>18</td>
      <td>79</td>
    </tr>
    <tr>
      <th>37</th>
      <td>c</td>
      <td>2</td>
      <td>93</td>
      <td>47</td>
    </tr>
    <tr>
      <th>81</th>
      <td>c</td>
      <td>1</td>
      <td>20</td>
      <td>44</td>
    </tr>
    <tr>
      <th>41</th>
      <td>c</td>
      <td>3</td>
      <td>12</td>
      <td>78</td>
    </tr>
    <tr>
      <th>10</th>
      <td>a</td>
      <td>2</td>
      <td>62</td>
      <td>92</td>
    </tr>
    <tr>
      <th>41</th>
      <td>a</td>
      <td>1</td>
      <td>37</td>
      <td>1</td>
    </tr>
    <tr>
      <th>94</th>
      <td>a</td>
      <td>3</td>
      <td>2</td>
      <td>18</td>
    </tr>
  </tbody>
</table>




## 2.5stack()和unstack()
stack: 将数据从”表格结构“变成”花括号结构“，即将其列索引变成行索引。

unstack: 数据从”花括号结构“变成”表格结构“，即要将其中一层的行索引变成列索引。


```python
import numpy as np
import pandas as pd
from pandas import Series,DataFrame
data=DataFrame(np.arange(12).reshape((3,4)),index=pd.Index(['street1','street2','street3']),
               columns=pd.Index(['store1','store2','store3','store4']))
print(data)
print('-----------------------------------------\n')
data2=data.stack()# 列索引[store1-store4] 都变成行索引
data3=data2.unstack() # 行索引(默认最里层) [store1-store4] 变成列索引
print(data2)
print('-----------------------------------------\n')
print(data3)
```

             store1  store2  store3  store4
    street1       0       1       2       3
    street2       4       5       6       7
    street3       8       9      10      11
    -----------------------------------------
    
    street1  store1     0
             store2     1
             store3     2
             store4     3
    street2  store1     4
             store2     5
             store3     6
             store4     7
    street3  store1     8
             store2     9
             store3    10
             store4    11
    dtype: int32
    -----------------------------------------
    
             store1  store2  store3  store4
    street1       0       1       2       3
    street2       4       5       6       7
    street3       8       9      10      11



```python
# 使用level = -1 是最内层；level = 0 是往外推一层
data4=data2.unstack(level=0) #这个例子中level = 0 就是最外层的行索引[street1-4]
print(data4)
```

            street1  street2  street3
    store1        0        4        8
    store2        1        5        9
    store3        2        6       10
    store4        3        7       11


## 2.6pivot()和pivot_table() 


```python
df = data2.reset_index()
df.rename(columns = {'level_0':'street','level_1':'store',0:'num'},inplace = True)
df
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>street</th>
      <th>store</th>
      <th>num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>street1</td>
      <td>store1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>street1</td>
      <td>store2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>street1</td>
      <td>store3</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>street1</td>
      <td>store4</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>street2</td>
      <td>store1</td>
      <td>4</td>
    </tr>
    <tr>
      <th>5</th>
      <td>street2</td>
      <td>store2</td>
      <td>5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>street2</td>
      <td>store3</td>
      <td>6</td>
    </tr>
    <tr>
      <th>7</th>
      <td>street2</td>
      <td>store4</td>
      <td>7</td>
    </tr>
    <tr>
      <th>8</th>
      <td>street3</td>
      <td>store1</td>
      <td>8</td>
    </tr>
    <tr>
      <th>9</th>
      <td>street3</td>
      <td>store2</td>
      <td>9</td>
    </tr>
    <tr>
      <th>10</th>
      <td>street3</td>
      <td>store3</td>
      <td>10</td>
    </tr>
    <tr>
      <th>11</th>
      <td>street3</td>
      <td>store4</td>
      <td>11</td>
    </tr>
  </tbody>
</table>



```python
# 长表变为宽表
df.pivot(index = 'street',columns=['store'],values='num')
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>store</th>
      <th>store1</th>
      <th>store2</th>
      <th>store3</th>
      <th>store4</th>
    </tr>
    <tr>
      <th>street</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>street1</th>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>3</td>
    </tr>
    <tr>
      <th>street2</th>
      <td>4</td>
      <td>5</td>
      <td>6</td>
      <td>7</td>
    </tr>
    <tr>
      <th>street3</th>
      <td>8</td>
      <td>9</td>
      <td>10</td>
      <td>11</td>
    </tr>
  </tbody>
</table>






