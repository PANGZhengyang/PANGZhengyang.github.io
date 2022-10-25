---
layout: post
title: "Python Base Study——Pandas"
date: 2020-04-23
description: "Python学习笔记"
tag: Python
---

This is my note for Python for Pandas. When I face some tricky problems, I'll record them.
Let's start!

# Pandas 数据输出显示设置

````python
pd.set_option('参数名',参数值)

```
参数名：
1.display.max_rows:最大显示行数，超过该值用省略号代替，为None时显示所有行。
2.display.max_columns:最大显示列数
3.display.precision: 数据小数点
4.display.max_colwidth: 每列最大字符宽度
```

pd.set_option('float_format', lambda x: '%.3f' % x) #取消科学计数法

# 还原所有显示设置
pd.reset_option('display')

# 忽略警告
import warnings
warnings.filterwarnings('ignore')

#基于style个性化设置
#隐藏索引列
.style.hide_index()

#标记缺失值 并 高亮
.style.set_na_rep('数据缺失').highlight_null(null_color='yellow')

#同时高亮数值列最大值和最小值
.style.highlight_max(color='blue').highlight_min(color='#26BE49')

# 修改字体颜色
.style.set_properties(subset=['salary'], **{'color': 'red'}).set_properties(subset=['matchScore'], **{'color': 'blue'})

# 导出样式
.style.set_properties(**{'background-color': '#F8F8FF','text-align':'center', 'font-size': '13px'})
.set_properties(subset=['salary'], **{'color': 'red'}).to_excel('带有样式导出.xlsx')

````

# Pandas 三大数据结构

1. Series：带`index`的一维`array`,特殊字典

   ```python
   pd.Series(data, index)
   # data: list,array, 标量 也可以为dict: keys-> index
   ```
   
2. DataFrame：二维数组，特殊字典，一个`key`映射一列`Series`数据

3. Index：不可变数组，有序几何

# 合并数据

```python
pd.concat([df1, df2], axis=0, ignore_index=False, join='outer', join_axes=['A', 'B'])
'''
axis=0 : 竖向链接
ignore_index=True ：重新建索引
join='inner' ：两个df2 都有的列名
join_axes=['A','B'] :合并A,B列

两个Series的合并也可以使用concat
'''

pd.merge(right, left, how='inner', on=None, left_on=None, right_on=None, left_index=None, right_index=None)
```

# Rename: 更改名字

```python
df.rename(index ={'a':1},inplace = True)
df.rename(columns = {'guiyang':2},inplace = True)

#如果只是改index的名字的话
df.index.name = xxx
df.rename_axis(index={'gender':'Gender'})
```
# Hierarchical Index: 多层索引
##  多层索引的创建


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

## 多层索引的取值

```python
#使用 .loc[]
df.loc['张三','期末']
```
```
    Jave      18
    Web       33
    Python    29
    Name: (张三, 期末), dtype: int32
```


## 多层索引的排序


```python
# 使用sort_index 为了展示的更好 用下面的例子
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
```

## set_index() 与 reset_index()
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


## stack()和unstack()
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
```


```python
# 使用level = -1 是最内层；level = 0 是往外推一层
data4=data2.unstack(level=0) #这个例子中level = 0 就是最外层的行索引[street1-4]
print(data4)
```
```

            street1  street2  street3
    store1        0        4        8
    store2        1        5        9
    store3        2        6       10
    store4        3        7       11
```

## pivot()和pivot_table() 


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

##  Crosstab
```python
#默认情况下，crosstab计算因子的频率表，比如用于str的数据透视分析
pd.crosstab(index,columns,values,aggfunc)

# 如果crosstab只接收两个Series，它将提供一个频率表。
pd.crosstab(df['A'],df['B']) #要写成index
```


# 缺失值

在做数据预处理是，会关注missing value：

1）查看缺失值：

```python
df.isnull()
#看每一列有数据的数量
df.info()
# 哪一列具有缺失值
df.isnull().any()

# 每列有多少缺失值
df.isnull().sum()

# 总共有多少缺失值
df.isnull().sum().sum()
```

2）定位哪些行具有缺失值：

```python
df.loc[df.isnull().values]
df.drop_duplicates() # 要加一步去重

# 第二种方法:
df[df.isnull().T.any()==True]
```

3）删除：

```python
df.dropna()

# 删除某列中含有缺失值的所在行
ddf.dropna(subset=['age'])
```

4）填充：

```python
#用xxx列的均值填充
df['xxx'].fillna(df['xxx'].mean())

#用插值法填充：缺失值的上下均值填充
df['xxx'] = df['xxx'].fillna(df['xxx'].interpolate())

```

# 重复值

在数据预处理的过程中，重复值也有可能使我们要关注的：

```python
df.duplicated() 

df.drop_duplicates(inplace=True)
```

# 切分数据

```python
# 等距切分 distance = (max-min)/bins
pd.cut(x, bins, right=True, labels=[])

# 等频切分 quantile,每个bin里面的数据量相等
pd.qcut(x, q, labels=[])
```
# 删除数据

## 删除行

```python
df.drop([0,1,2]) #删除第1,2,3行，默认axis=0
```

## 删除列

```python
df.drop('name', axis=1) #删除name列，axis=1为列
```

# Groupby 与窗口函数

```python
df = pd.DataFrame({'Country':['China','China', 'India', 'India', 'America', 'Japan', 'China', 'India'],
                   'Income':[10000, 10000, 5000, 5002, 40000, 50000, 8000, 5000],
                    'Age':[5000, 4321, 1234, 4010, 250, 250, 4500, 4321]})
print(df)

'''
   Country  Income   Age
0    China   10000  5000
1    China   10000  4321
2    India    5000  1234
3    India    5002  4010
4  America   40000   250
5    Japan   50000   250
6    China    8000  4500
7    India    5000  4321
'''

# groups可以得到类别，属于类别的index
df.groupby('Country').groups

# {'America': [4], 'China': [0, 1, 6], 'India': [2, 3, 7], 'Japan': [5]}

# groupby之后得到的是一个可迭代对象
for index, data in df.groupby('Country'):
    print(index)
    print(data)
  
'''
America
   Country  Income  Age
4  America   40000  250
China
  Country  Income   Age
0   China   10000  5000
1   China   10000  4321
6   China    8000  4500
India
  Country  Income   Age
2   India    5000  1234
3   India    5002  4010
7   India    5000  4321
Japan
  Country  Income  Age
5   Japan   50000  250
'''

# 通过 get_group() 获取的想要的组
df.groupby('Country').get_group('China')
```

````python

```
groupby() 	 --> aggreate({'A':np.sum,'B':np.median}) / aggreate([np.sum, np.median])
			--> fliter(函数)
			--> transform(函数) 对groupby后的dataframe中的每个数据进行func运算，返回运算后的dataframe
			--> apply(函数)
```
# .agg
# 实现分组非重复计数 nunique函数用于非重复计数
df.groupby(['city']).agg({'num':pd.Series.nunique})


# .transform
# 例如在china这个组里面发现最大值，然后China这个组每一行都记录这个值
df['transform_income'] = df.groupby('Country')['Income'].transform(np.max)

'''
   Country	Income	 Age	transform_income
0	China	10000	5000	10000
1	China	10000	4321	10000
2	India	5000	1234	5002
3	India	5002	4010	5002
4	America	40000	250	    40000
5	Japan	50000	250	    50000
6	China	8000	4500	10000
7	India	5000	4321	5002
'''

# pandas 实现hive 窗口函数
df = pd.DataFrame({'age':[12,20,12,5,18,11,18],
                   'name':['A','B','A','B','B','A','A']})
df['row_number'] = df['age'].groupby(df['name']).rank(ascending=True,method='first')
print(df)

'''
rank(ascending=True, method= ) 
'first' = row_number 1,2,3,4
'dense' = dense_rank 1,2,2,3
'min' = rank 1,2,2,4
'''
````

# 排序

```python
sort_index()
sort_values(by = '某一列')
```

# 时间序列

## pandas时间序列的数据结构

- Timestamp：时间戳，某一刻的时间。`pd.Timestamp()` -> `DatetimeIndex`
- Period: 时期，一段时间。->`PeriodIndex`
- Timedelta: 时间间隔。 ->`TimedeltaIndex`

## 时间戳

```python
pd.Timestamp()
pd.to_datetime()
# 如果传入单一时间：Timestamp
# 如果传入多个时间： DatetimeIndex

pd.date_range()
# pd.date_range(start=None, end=None, periods=None, freq='D', tz=None, normalize=False, name=None, closed=None, **kwargs)
# start：开始时间
# end：结束时间
# periods：偏移量
# freq：频率，默认天，pd.date_range()默认频率为日历日，pd.bdate_range()默认频率为工作日
# tz：时区
# normalize：时间参数值正则化到午夜时间戳（这里最后就直接变成0:00:00，并不是15:30:00）
# name：索引对象名称
# closed：默认左闭右闭 left左闭右开

pd.bdate_range() #工作日

#将时间转换为字符串
dt.strftime('%Y-%m-%d') 

#将字符串转换为时间戳
datetime.strptime('2021-01-01 12:00:00')

#频率转换
.asfreq()
```

## 时期Period

```python
# 时间戳转时期
pd.to_period()

# 时期转时间戳
pd.to_timestamp()

# 重采样
# resample() 一个频率转换为另一个频率，数据会发生结合，累计值
resample('5D').sum() #5天总和
asfreq('5D') #最后一天的值

#重采样中有升采样和降采样 ：
#升采样比如1天分成多个小时：这个时候会增加数据，所以要考虑数据的填充
#降采样比如天变成月：这个时候可以取累计，也可以取第一条或最后一条
```

![2020-04-23-python-Base-Study-pandas-1](/assets/python notes/pandas/2020-04-23-python-Base-Study-pandas-1.png)

# 高性能Pandas

## pandas.eval()实现高性能计算

```python
# pd.eval() 用字符串代数式实现DataFrame的高性能基计算
import pandas as pd
nrows, ncols = 100000, 100
rng = np.random.RandomState(42)
df1, df2, df3, df4 = (pd.DataFrame(rng.rand(nrows, ncols))
                      for i in range(4))

pd.eval('df1+df2+df3+df4')
```

## pandas.query()获取数据

```python
df.query(expr，inplace = False) 
eg: df[(df['a']>10) & (df['b']<20) ] 和 df.query(' a >10 and b <20') 等价
```

# 输出Excel

```python
with pd.ExcelWriter('path + excel_name') as writer:
    data1.to_excel(writer, sheet_name='', index=False)
    data2.to_excel(writer, sheet_name='', index=False)
```

# Pandas.apply

## 遍历DataFrame的元素

`dataframe.apply(function, axis)` 对一行或一列做出一些操作(`axis=1` 遍历行，`axis=0`遍历列)

注意：是逐行逐列应用该函数

```python
df1 = pd.DataFrame([[3,5],[4,8]], columns=list('AB'))
df1
```

```
    A	B
0	3	5
1	4	8
```

```python
# 对行进行遍历
df1.apply(np.sum, axis=1)

'''
0     8
1    12
'''

# 默认对列进行遍历
df1.apply(np.sum)
'''
A     7
B    13
'''
```

## 遍历Series的元素

```python
df1['C'] = df1['A'].apply(lambda x: 111 if x<=3 else 900)
df1

'''
     A	B	C
0	3	5	111
1	4	8	900
'''
```

## 使用lambda表达式

第一种可以使用自定义函数

```python
# 关于多个if：
df1['D'] = df1['A'].apply(lambda x: 1 if x<2 else (2 if x<4 else 3 ))
df1

#自定义函数
def f(x):
    if x<2:
        return 1
    elif x<4:
        return 2
    else:
        return 3

df1['E'] = df1['A'].apply(lambda x: f(x))
df1

'''
    A	B	C	D	E
0	3	5	111	2	2
1	4	8	900	3	3
'''
```

如果要对多列进行处理：

```python
'''
L为语言，如果语言不为空就用该语言，L为空，则根据所属地来判断语言
'''
def f(x):
    if x['L'] !='Nan' :
        return x['L']
    else:
        if x['R'] == '美国':
            return ('英语')
        else:
            return (str(x['R'])+ '语')

df['语言1'] = df.apply(lambda x: f(x),axis=1) #这个地方加上axis=1 表示横向处理
df
```

# Pandas.applymap

`dataframe.applymap(function)` 对pandas对象**逐元素**应用某个函数，成为元素级函数应用：

`applymap` 与`map` 的区别：

- `applymap()` 是Dataframe的实例方法
- `map()` 是Series的实例方法

```python
df.applymap(lambda x: '%.2f'%x) # dataframe
df['某列'].map(lambda x: '%.2f'%x) # series
```
# Dataframe遍历

`dataframe.iterrows()`返回每一行的index，以及该行的数据

`dataframe.iteritems()`返回每一列的columns名，以及该列的数据

```python
import pandas as pd
import numpy as np
df = pd.DataFrame(np.random.randn(10, 4), columns=list('ABCD'))

for index, row in df.iterrows():
    print(index)
    print(row)
    
for columns, row in df.iteritems():
    print(columns)
    print(row)
```




# EDA

```python
import pandas_profiling

pandas_profiling.ProfileReport(df)
```

```python
import sweetviz as sv

report = sv.analyze(df)
report.show_html()
```



