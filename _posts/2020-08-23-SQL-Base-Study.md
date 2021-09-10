---
layout: post
title: "SQL Base Study-Impala/Hive"
date: 2021-08-23
description: "SQL"
tag: SQL
---

关于Impala的总结~

# 1、建表逻辑

## 1.1 Impala 建表与drop表

主要参考 Capriolo, Wampler, and Rutherglen (2012) 的书

```sql
drop table if exists test.xxx;

create table if not exists test.xxx stored as parquet as 
select ... from ...;

```

## 1.2 show stats(Cloudera, Inc. 2018b)

`compute stats` 可以使得表的`join`等指令运行加快.

## 1.3 insert table

在impala里，没有update的用法：

- `insert overwrite table` :delete and update

- `insert into table` :update

  由于涉及分区，所以更新data一般会先取之前的数据再加上今天的数据：

  ```sql
  insert overwrite test.xxx
  select x1,x2 
  from test.xxx 
  where dt < to_date(now())
  union all
  select x1,x2
  from a
  where dt = to_date(now())
  ```

# 2.连接表

## 2.1 多层嵌套

  关于这一点，有很多code会出现subquery，一个比较好的实践是使用`with... as`结构

## 2.2 semi/anti join

  `left semi join` 是` in / exists` 子查询的一种更高效的实现，只返回左表的数据

  ```sq
  select A.key,A.value
  from A
  where A.key in (select B.key from B)
  
  -- 改写为
  select A.key, A.value
  from A
  left semi join B on A.key = B.key
  ```

  ` left anti join` 是 `not in /exists` 子查询的一种高效地实现

  ```sql
  select A.key,A.value
  from A
  where A.key not in (select B.key from B)
  
  -- 改写为
  select A.key, A.value
  from A
  left anti join B on A.key = B.key
  ```

### 2.2.1 in 和exists的用法

  先说一说exists和in的执行机制和顺序：

  - exists：通常后面会加一个相关子查询Subquery:

    ```SQL
    SELECT c.CustomerId, CompanyName 
    FROM Customers c 
    WHERE EXISTS( SELECT OrderID FROM Orders o WHERE o.CustomerID = cu.CustomerID)
    ```

    subquery并不会返回结果集，而是True/False（这就是为什么可以select 任何东西）；其运行方式是：

    1. 首先执行一次外部查询

    2. 对于外部查询的每一行分别去执行一次子查询（相当于带着上述代码中c表中的一条数据 去subquery 中 根据条件 `WHERE o.CustomerID = cu.CustomerID` 去对应，如果有的话就会返回True，从而输出）

    3. 使用子查询的结果来确定外部查询的结果集

       **exists 适合外表小内表大**

  - in: `in ()` 的子查询是返回结果集的，所以子查询会先产生结果集，然后主查询再去结果集里去寻找符合要求的字段，符合要求就输出

    ​	   **in适合外表大内表小**
    
    ### 2.2.2  [What are the biggest feature gaps between HiveQL and SQL? - Quora](https://www.quora.com/What-are-the-biggest-feature-gaps-between-HiveQL-and-SQL) 
    
    No regular INTERSECT, or MINUS operators
    
    
    

# 3.运算符

## 3.1 like/rlike/ilke的区别
    like是通配符:不区分大小写;
    ilike:区分大小写；
    rlike是正则，和regexp相同 正则相关知识请参考[正则表达式](https://blog.csdn.net/weixin_40907382/article/details/79654372)

  




  # 4.函数 Functions

## 4.1 Built-in functions

### 4.1.1  Others （(Capriolo, Wampler, and Rutherglen 2012, 83–84)

- `round()`
- `floor()` 地板函数 `floor(2.6) = 2`
- `ceiling()` 天窗函数 `ceil(2.6) = 3`
- cast ('xxx' as int)

### 4.1.2 Times

#### 4.1.2.1 from_unixtime()

```sql
from_unixtime(t1,'yyyy-MM-dd HH:mm:ss')
```

其中t1是10位的时间戳值，即1970-1-1至今的秒，而13位的所谓毫秒的是不可以的。 对于13位时间戳，需要截取，然后转换成bigint类型，因为from_unixtime类第一个参数只接受bigint类型。

```sql
select from_unixtime(cast(substring(tistmp,1,10) as bigint),’yyyy-MM-dd HH’) tim ,
		count(*) cn 
from ttengine_hour_data where …
```

#### 4.1.2.2 unix_timestamp()

1. `UNIX_TIMESTAMP() `：若无参数调用，则返回一个 Unix timestamp ('1970-01-01 00:00:00' GMT 之后的秒数) 作为无符号整数，得到当前时间戳;
2. `UNIX_TIMESTAMP(date) `：若用date 来调用 UNIX_TIMESTAMP()，它会将参数值以'1970-01-01 00:00:00' GMT后的秒数的形式返回。date 可以是一个 DATE 字符串、一个 DATETIME字符串、一个 TIMESTAMP或一个当地时间的YYMMDD 或YYYMMDD格式的数字。

两个函数通常搭配使用：

```sql
select from_unixtime(unix_timestamp('2021-08-09')+3600*24,'yyyy-MM-dd'); --8月9日加一天
```

#### 4.1.2.3 add_?*

1. `add_months` :返回的是timestamp 可以试用`to_date()` 转换 

   ```sq
   select add_months('2021-08-09',1);
   select to_date(add_months(now(),1));
   ```

2. `date_add`: 返回的是timestamp 

   ```sql
   select date_add('2021-08-09',1)
   ```

3. `date_sub` 

#### 4.1.2.4 时间提取

```sql
select date_trunc('month','2021-08-01')
```

#### 4.1.2.5 cast(... as timestamp) 转换成时间戳

#### 4.1.2.6 一些特殊的写法

```sql
-- 三个月前1号开始
select   to_date(date_trunc('month', months_sub(date_sub(now(), 1), 2)));

-- 本月1号
to_date(concat(substr(now(),1,7),'-01'))
```

#### 4.1.2.7 时间差

`datediff(timestamp enddate, timestamp startdate)`(mysql也可以用)(Cloudera 2018a)

 #### 4.1.2.8 where 条件中时间比较时，string格式默认转为时间格式

```sql
with tbl_2 as
(
    with tbl_1 as
    (
      select '2018-01-01 08:00:00' as tm union all
      select '2018-01-02 08:00:00' as tm union all
      select '2018-01-01 09:00:00' as tm union all
      select '2018-01-03 08:00:00' as tm union all
      select '2018-01-01 10:00:00' as tm union all
      select '2018-01-04 08:00:00' as tm union all
      select '2018-01-01 13:00:00' as tm union all
      select NULL as tm
    )
    select cast(tm as timestamp) as tm
    from tbl_1
)
select tm
from tbl_2
where tm > '2018-01-02'

-- 等价
with tbl_2 as
(
    select '2018-01-01 08:00:00' as tm union all
    select '2018-01-02 08:00:00' as tm union all
    select '2018-01-01 09:00:00' as tm union all
    select '2018-01-03 08:00:00' as tm union all
    select '2018-01-01 10:00:00' as tm union all
    select '2018-01-04 08:00:00' as tm union all
    select '2018-01-01 13:00:00' as tm union all
    select NULL as tm
)
select tm
from tbl_2
where tm > '2018-01-02'
```

### 4.1.3 正则

### 4.1.4 文本函数

#### 4.1.4.1 split in Hive

在impala里面使用 `split_part(string source, string delimiter, bigint n)` (Cloudera 2018c)。

```sql
select split_part('A,B,C',',',1);
```

#### 4.1.4.2 合并文本

`concat_ws(sep,s1,s2,...)`

```sql
select concat_ws(',','a','b');
```

`concat()`

```sql
select concat('2020','-01');
```

#### 4.1.4.3 替换、去掉空格

```sql
-- 首尾
select trim('  sdfs  ');

-- 使用replace 可以去掉中间的
select replace('s d f',' ','');
```

`replace(string_expression, string_pattern, string_replacement)`

#### 4.1.4.4 截取

`substr(str,start,end)`

## 4.2 汇总函数 Aggregate Functions

`max,min,count,avg` 这个地方注意一下·`null`

1. `count('column')` :如果column 有null 不会计数的；但是`count(1),count(0),count(*)` 是计的

## 4.3分析函数 Analytic Functions

Analytic functions are usually used with `OVER`, `PARTITION BY`, `ORDER BY`, and the windowing specification. `Function (arg1,..., argn) OVER ([PARTITION BY <...>] [ORDER BY <....>] [<window_clause>])` (D. Du 2015, 章节6) 开窗函数

- `row_number()` :1,2,3,4

- `rank()`:1,1,3,4

- `dense_rank()`:1,1,2,3

- `lead()`和`lag()` : lag() 取上

- `first_value` 取分组内排序后，截止到当前行，第一个值

- `last_value`  取当前行数据与当前行之前的数据的比较，如果需要在结果的所有行记录中输出同一个满足条件的最后一个记录，在order by 条件的后面加上语句：**rows between unbounded preceding and unbounded following**。

- `cumsum()` 累计求和 

- `sum() count() min() max() avg()` 

  `sum（c）over()` -直接给出全部求和

  `Sum (c ) over(order by b)` -累加

  `Sum( c) over(partition by b)` 直接给出分后的结果【没有order就没有累加】

### 4.3.1 `[PARTITION BY <...>] s`

类似于`group by`

### 4.3.2 `[ORDER BY <....>]`

类似于`order by`

### 4.3.3 `[<window_clause>]`

最后function的数据范围，是current row的前多少后多少可以满足。

- `rows between current row and 1 following`
- `rows between 2 preceding and current row`
- `rows between 2 preceding and unbounded following`
- `rows between 1 preceding and 2 preceding`
- `rows between unbounded preceding and 2 preceding`

解释一下

- unbounded：无界限
- preceding：从分区第一行头开始，则为 unbounded。 N为：相对当前行向前的偏移量
- following ：与preceding相反,到该分区结束，则为 unbounded。N为：相对当前行向后的偏移量
- current row：顾名思义，当前行，偏移量为0

> If we omit BETWEEN…AND (such as `ROWS N PRECEDING` or `ROWS UNBOUNDED PRECEDING`), Hive considers it as the start point, and the end point defaults to **the current row**. (D. Du 2015)

