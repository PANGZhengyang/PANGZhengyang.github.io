---
layout: post
title: "Impala性能优化"
date: 2021-03-02
description: "SQL"
tag: SQL
---

# 1、Impala

Impala基于Hive，提供对HDFS、HBase数据的高性能、低延迟的交互式SQL查询功能

# 2、Impala性能优化常用思路

## ## 2.1 explain查看impala执行计划

> You can see the results by looking at the **EXPLAIN** plan for a query, without the need to actually run it. （Russell (2014)） 

不用实际执行sql，使用explain看执行计划

> To understand the flow of the query, you read from bottom to top. (After checking any warnings at the top.) For a join query, you prefer to see the biggest table listed at the bottom, then the smallest, second smallest, third smallest, and so on.

从bottom开始往上看

```[localhost:21000] > explain select count(*) from partitioned_normalized_parquet
> join stats_demo_parquet using (id)
> where
> substr(partitioned_normalized_parquet.name,1,1) = 'G';
+-------------------------------------------------------------------------+
| Explain String                                                                            |
+-------------------------------------------------------------------------+
| Estimated Per-Host Requirements: Memory=194.31MB VCores=2                                 |
|                                                                                           |
| 06:AGGREGATE [MERGE FINALIZE]                                                             |
| | output: sum(count(*))                                                                   |
| |                                                                                         |
| 05:EXCHANGE [UNPARTITIONED]                                                               |
| |                                                                                         |
| 03:AGGREGATE                                                                              |
| | output: count(*)                                                                        |
| |                                                                                         |
| 02:HASH JOIN [INNER JOIN, BROADCAST]                                                      |
| | hash predicates: oreilly.partitioned_normalized_parquet.id =                            |
| |     oreilly.stats_demo_parquet.id                                                       |
| |                                                                                         |
| |--04:EXCHANGE [BROADCAST]                                                                |
| | |                                                                                       |
| | 01:SCAN HDFS [oreilly.stats_demo_parquet]                                               |
| |      partitions=27/27 size=26.99MB                                                      |
| |                                                                                         |
| 00:SCAN HDFS [oreilly.partitioned_normalized_parquet]                                     |
|     partitions=27/27 size=22.27GB                                                         |
|     predicates: substr(partitioned_normalized_parquet.name, 1, 1) = 'G' |
+-------------------------------------------------------------------------+
Returned 21 row(s) in 0.03s
```

查看这段sql执行计划，`partitions = 27/27 size=22.27GB` 扫描了全表，而未进行分区裁剪

## 2.2 分区裁剪 

 ### 2.2.1 Impala每一个分区都有一个文件夹，查看有无分区：

```sql
show partitions edw.d_cs_cdr_dly;
```

若无分区会报错

### 2.2.2 分区裁剪作用

基于频繁查询的列中不同的值对数据进行物理划分， 查询可以更快地跳过表中大部分数据

### 2.2.3 通过explain判断分区裁剪

**两个原则（且关系）**

- 在`where` 和`join ..on`的on中对分区字段做了限制
- 限制是不需要查表中的数据就知道的

e.g.1 没有做分区裁剪

```sql
select  user_id 
from edw.d_cs_cdr_dly 
where dt > (select start_dt from test.date_range limit 1) 
and dt <= (select end_dt from test.date_range limit 1)
```

e.g.2 在 left join 里面对左表做限制是没有效果的所以还是没有做到分区裁剪

```sql
select
a.user_id
, a.dt
from edw_s.common_user_daily a
left join edw.list_loan_snp b
on a.user_id = b.borrowerid
and a.dt = b.dt
and a.dt = '2021-08-10';
```

![2021-03-02-SQL-impala-optimize](/assets/2021-03-02-SQL-impala-optimize.png)

### 2.2.4 创建分区表

```sql
create table test.xxx(
uid int
) partitioned by (dt string)
stored as parquet
;
```

or

```sql
create table test.xxx partitioned by (dt) stored as parquet as
select uid,
		dt -- 很重要
from test.x1
```

### 2.2.5 修改分区表

通常使用``insert overwrite`

```sql
insert overwrite table test.xxx partition(dt)
select
*
from test.x1
where dt ='2021-09-01'
```

如果数据是每天更新，最佳实践是：

```sql
insert overwrite table test.xxx
select * from test.xxx
where dt < to_date(now())
union all
select * from test.x1
where dt = to_date(now())
```

## 2.3 合适的join

在impala中执行一条sql的旅程（简化）

1. 表的数据存储在分布式文件系统里 一张表的数据可以存储在多台机器上 
2. 一个SQL会被拆解为多个任务执行 并由多个worker参与计算 计算是分布式的，worker可能分布在 不同机器上 
3. worker通过读取文件来获取表数据

![2021-03-02-SQL-impala-optimize1](D:\庞正扬\Github\PANGZhengyang.github.io\_posts\2021-03-02-SQL-Impala-optimize .assets\2021-03-02-SQL-impala-optimize1.png)

### 2.3.1 大表join大表

用partitioned join：在`join`或`left join` 后面加上`[shuffle]`即可

![2021-03-02-SQL-impala-optimize2](/assets/2021-03-02-SQL-impala-optimize2.png)

```sql
SELECT 
….
FROM 
join_left_hand_table
JOIN [SHUFFLE] 
join_right_hand_table
```

### 2.3.2 小表join 大表

用`broadcast join`

![2021-03-02-SQL-impala-optimize3](/assets/2021-03-02-SQL-impala-optimize3.png)

## 2.4 parquet

## 2.5 compute stats

