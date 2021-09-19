---
layout: post
title: "Ideas about sql"
date: 2020-09-23
description: "SQL"
tag: SQL
---

# 一、找重复的问题

**group by** 和 **having （count（）>1）**.
where 字句不能和聚合函数一起使用，因为 where 字句被执行的顺序比 group 前，所以运行到 where 时还没被分组 

# 二、每组最大的 n 条记录;排名问题： 

1）rank（）over(partition by... order by ...) --1,2,2,4 
 Row_number() over(partition by... order by ...) --1,2,3,4
Dense_rank()over (partition by... order by ...) ---1,2,2,3
2）用 max，min ；先求最大值，在剔除
3）用 order by + limit y offset x 跳过 x 条数据读取 y 条 

# 三、各种查询方式：

![在这里插入图片描述](https://img-blog.csdnimg.cn/1260a66bf7614b45b9b7c4dac81a5ef9.jpg?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)

# 四、union连接

t1是购买商品的表；t2是收藏商品的表
uid-用户id； cid-商品id
![在这里插入图片描述](https://img-blog.csdnimg.cn/779fae13e30d4bc087a80d4197bdf802.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_20,color_FFFFFF,t_70,g_se,x_16)
将上述表变为下面的形式：
![在这里插入图片描述](https://img-blog.csdnimg.cn/6ffd97aaa8814c6cb1d0a92f9c25fcf5.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_20,color_FFFFFF,t_70,g_se,x_16)

```sql
select  t1.uid,
		t1.cid,
		1 as 'Buy',
		case when t2.uid is null then 1 else 0 end as 'Buy and not collect',
		0 as 'collect and not buy'
		case when t2.uid is not null then 1 else 0 end as 'Buy and collect'	  
from t1 
left join t2 on t1.uid = t2.uid and t1.cid = t2.cid
union
select  t2.uid,
		t2.cid,
		case when t1.uid is not null then 1 else 0 end as 'Buy',
		0 as 'Buy and not collect',
		case when t1.uid is null then 1 else 0 end as 'collect and not buy',
		case when t1.uid is not null then 1 else 0 end as 'Buy and collect'
from t2
left join t1 on t2.uid = t1.uid and t2.cid = t1.cid
```

***注意*** Union连接两个select时，要确保两个select的字段是相同的！！！

# 五、lag/ lead窗口函数的运用

## 5.1 lag/lead基础知识

首先说一下lag窗口函数：
lag(col,n,default) over(partition by ... order by ...)
col - 哪一列
n - 往上取多少条
default - 如果往上取没有的话用什么填充
lag(createtime,1,null) over(partition by cookieid order by createtime) as dt
相当于第二列要取它上面一列的
![在这里插入图片描述](https://img-blog.csdnimg.cn/40e545cf0cd74aaaad37907d1ddd43e1.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_12,color_FFFFFF,t_70,g_se,x_16)
例1：pay表记录每天的营业额(amount)，现在要取比前两天营业额都多的日期(dt)：
![在这里插入图片描述](https://img-blog.csdnimg.cn/f0c8c20724e94c17b918a145a2eb1256.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_20,color_FFFFFF,t_70,g_se,x_16)

```sql
with t as (
select dt,amount,
		lag(amount,1,null) over(order by dt) as last_amount1
		lag(amount,2,null) over(order by dt) as last_amount2
from pay
)
select dt,amount
from t
where amount >last_amount1 and amount >last_amount2
```

## 5.2 lag/lead 常见场景

1）求环比

```sql
amount / lag(amount,1) over(order by dt) -1  as '环比'
```

2）用户在app两次点击的时间间隔

```sql
lead(ts,1) over(partition by user_id order by ts) - ts
```

# 六、case when用法

## 6.1相邻两行交换

![在这里插入图片描述](https://img-blog.csdnimg.cn/cfb0f7fd0f7e40d48716e43fa539f1c3.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_20,color_FFFFFF,t_70,g_se,x_16)

```sql
with t as (
select count(*) as cnt from seat
)
select case when mod(id,2) !=0 and id != cnt then id+1
			when mod(id,2) !=0 and id = cnt then id
			else id -1 as id2,
			student
from seat,t
```

## 6.2 行列互换

![在这里插入图片描述](https://img-blog.csdnimg.cn/6bc749f91aeb4a819f1b957b83c3666a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_20,color_FFFFFF,t_70,g_se,x_16)

```sql
select year,
		max(case when month = 1 then score else 0 end) as Jan,
		Max(case month when 2 then score else 0 end) as Feb,
		Max(case month when 3 then score else 0 end) as Mar,
		Max(case month when 4 then score else 0 end) as Apr
from YMD
group by year
```

如果要列转为行：union

```sql
select year, 
		1 as 'month',
		Jan as score
from YMD1
union
select year,
		2 as 'month',
		Feb as score
		...
```

补充一个在hive中比较好用的函数：map：

```sql
create table test.T0919
(
    id int,
    yes int,
    noo int
);

insert into test.T0919 values
(1,21,89),
(2,11,65);

select id,map('yes',yes,'noo',noo) as tep_column
from test.T0919
```

```
id   tep_column
1     {'yes':21,'noo':89}
2     {'yes':11,'noo':65}
```
使用侧视图以及`explode`函数将`map`拆开
```sql
with t as (
select id,map('yes',yes,'noo',noo)as tep_column
from test.T0919)

select id,
        bool,
        value
from t
lateral view explode(tep_column) a as bool,value;
```

```
id   bool  value
1      yes   21
1       no   89
2       yes  11
2       yes  65
```



## 6.3 case when 巧用:减少代码量

当需要按条件汇总时：
1）条件写到where里，改变了数据的行数。
2）条件写到case when里，不改变数据的行数，把属性值转换成度量值（聚合函数+case when）

```sql
sum + case when ... 计算某条件总数
avg + case when ... 计算比率
```

# 七、求众数

1）求每个数的count as cnt；
2）求max（count）as m_cnt
3）cnt = m_cnt

```sql
create table test.pzy_aaa(
    id int
);

insert into test.pzy_aaa(id) values
(1),(2),(3),(1),(2),(4),(5),(6),(7),(3),(4),(5),(3),(4),(9),(3),(3),(4),(3);

with t as (
select id,count(id)as cnt
from test.pzy_aaa
group by id),

     t1 as (
         select max(cnt) as max_cnt
         from t
     )

select distinct id
from t,t1
where cnt = max_cnt;
```

# 八、求中位数

根据上一道题的表：

```sql
with t as (
    select id,
           count(id) over() as cnt,
           row_number() over (order by id)as rnd
    from test.pzy_aaa
)

select avg(id) as median
from t
where rnd in (floor((cnt+1)/2),ceiling((cnt+1)/2))
```

# 九、计算留存率

留存率用来衡量用户的粘性
一般互联网常用的留存率包括：
次留：第二天留存的用户数/某天用户数
三留：
七留
现在该手机厂商想要分析手机中的应用（**相机**）的活跃情况，需统计次留、三留、七留
要用到的字段：用户id-uid、应用名称-app、登陆时间-dt
表是login
![在这里插入图片描述](https://img-blog.csdnimg.cn/e6b73ac70eb741efa15ecb160d44d41a.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAeW91bmd5YW5ncGFuZw==,size_20,color_FFFFFF,t_70,g_se,x_16)

```sql
with t as (
    select distinct uid, dt
    from login
    where app = '相机'
),

     tt as (
         select uid, t1.dt as dt1,t2.dt as dt2,
                datediff(t2.dt,t1.dt) as gap
         from t as t1
         left join t as t2 on t1.uid = t2.uid
     )
select dt1,
       count(distinct uid),
       count(distinct case when gap = 1 then uid else null end) /count(distinct uid) as '次留',
       count(distinct case when gap = 3 then uid else null end) / count(distinct  uid) as '三留',
       count(distinct  case when gap =7 then uid else null end) / count(distinct uid) as '七留'
from tt
group by dt1
```

#  十、计算连续登录的人数

找到至少连续登陆3天的id：

```sql
drop table if exists test.tmp_continous;

create table test.tmp_continous
(
    id STRING ,
    dt string --impala:string格式表示时间会更加好用
);

INSERT OVERWRITE TABLE  test.tmp_continous
Select '201', '2017-01-01 00:00:00' union all
Select '201','2017-01-02 00:00:00' union all
Select '202','2017-01-02 00:00:00' union all
Select '202','2017-01-03 00:00:00' union all
Select '203','2017-01-03 00:00:00' union all
Select '201','2017-01-04 00:00:00' union all
Select '202','2017-01-04 00:00:00' union all
Select '201','2017-01-05 00:00:00' union all
Select '202','2017-01-05 00:00:00' union all
Select '201','2017-01-06 00:00:00' union all
Select '203','2017-01-06 00:00:00' union all
Select '203','2017-01-07 00:00:00';


with drop_duplicate as (
    select distinct id, dt
    from test.tmp_continous
),
     t1 as (
         select id, dt,row_number() over (partition by id order by dt) rnd
         from drop_duplicate
     ),

     t2 as (
         select id, dt, date_sub(dt,rnd) as dt1
         from t1
     )
select id
from t2
group by id,dt1 having count(dt1) >=3
order by id;
```

如果要找到每个id最大连续登陆天数：只是在上述代码稍作修改。

```sql
with drop_duplicate as (
    select distinct id, dt
    from test.tmp_continous
),
     t1 as (
         select id, dt,row_number() over (partition by id order by dt) rnd
         from drop_duplicate
     ),

     t2 as (
         select id, dt, date_sub(dt,rnd) as dt1
         from t1
     ),

     t3 as (
         select id,count(dt1) as cnt
        from t2
        group by id,dt1
        order by id
     )

select id , max(cnt) as '连续登录最大天数'
from t3
group by id;
```

对于连续性的题目，有几个常见的思路：自连接、窗口函数的运用：lag/lead 、 row_number

查询出最大连续失败场次数大于3的记录。

```sql
CREATE TABLE test.T0823 (
ID INT,
changci INT,
outcome string
);

INSERT INTO test.T0823 VALUES(1,34,'败');
INSERT INTO test.T0823 VALUES(2,35,'胜');
INSERT INTO test.T0823 VALUES(3,36,'胜');
INSERT INTO test.T0823 VALUES(4,37,'败');
INSERT INTO test.T0823 VALUES(5,38,'败');
INSERT INTO test.T0823 VALUES(6,39,'败');
INSERT INTO test.T0823 VALUES(7,40,'败');
INSERT INTO test.T0823 VALUES(8,41,'胜');
INSERT INTO test.T0823 VALUES(9,42,'胜');

with t1 as(
select *,row_number() over (order by id) num1
from test.T0823),
     t2 as(
         select *,
                num1 - row_number() over (partition by outcome order by outcome) num2
         from t1
     )
select id,changci,outcome
from t2
where num2 in (
    select num2
    from t2
    group by num2
    having count(num2) >3
    )
```

