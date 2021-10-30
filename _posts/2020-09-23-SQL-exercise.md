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

有些时候想实现行转列的的需要，如下test表

```
col1   col2   col3
a       b       1
a       b       2
a       b       3
c       d       4
c       d       5
c       d       6
```

变为

```
a       b       1,2,3
c       d       4,5,6
```

这个时候可以使用`concat_ws`和`collect_set`：

```sql
select col1,col2, concat_ws(',',collect_set(col3))
from test
group by col1,col2;
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

有一种特殊情况：

| NUM  | QTY  |
| :--: | :--: |
|  1   |  2   |
|  5   |  3   |

变为：

| NUM  |
| :--: |
|  1   |
|  1   |
|  5   |
|  5   |
|  5   |

提供一个思路：使用`repeat`和`explode`

```sql
create table test.T0810 (num int, QTY int);
insert into test.T0810 values(1,2);
insert into test.T0810 values(5,3);

with t as (
select repeat(concat(cast(num as string),','),qty) as num
from test.T0810),

t1 as (
select explode(split(num,',')) as num
from t)

select *
from t1
where num !='';
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

例1：查询出最大连续失败场次数大于3的记录。

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

例2：取到每段连续日期的起始日期，终止日期，持续天数以及起始日期距上一期终止日期间隔的天数。

```sql
CREATE TABLE test.T0818
(
ID INT,
RQ string
);

INSERT into test.T0818 VALUES(1,'2020-01-01');
INSERT into test.T0818 VALUES(2,'2020-01-02');
INSERT into test.T0818 VALUES(3,'2020-01-03');
INSERT into test.T0818 VALUES(4,'2020-01-06');
INSERT into test.T0818 VALUES(5,'2020-01-07');
INSERT into test.T0818 VALUES(6,'2020-01-10');
INSERT into test.T0818 VALUES(7,'2020-01-11');
INSERT into test.T0818 VALUES(8,'2020-01-12');
INSERT into test.T0818 VALUES(9,'2020-01-19');
INSERT into test.T0818 VALUES(10,'2020-01-20');
INSERT into test.T0818 VALUES(11,'2020-01-22');
INSERT into test.T0818 VALUES(12,'2020-01-23');
INSERT into test.T0818 VALUES(13,'2020-01-28');

with t0 as (
select min(rq) begin_rq,
       max(rq) end_rq,
       datediff(max(rq),min(rq))+1 as duration_day,
       row_number() over (order by min(rq)) rn
from test.T0818
group by  to_date(date_sub(rq,id))),

     t1 as (
select a.begin_rq,
         a.end_rq,
         a.duration_day,
         lead(a.begin_rq,1) over(order by a.begin_rq) next_rq
from t0 a )

select begin_rq,end_rq,duration_day,datediff(next_rq,end_rq) as gap
from t1;

```

# 十一、货拉拉互联网物流场景

有如下两张表：

![2020-09-23-SQL-exercise](/assets/2021-09-23-SQL-exercise-1.png)

```sql
-- 用车方和司机被禁止(banned=1)的比率分别为？
select role,
       sum(case when banned=1 then 1 else 0 end) /count(role)
from hll_t2
group by role;

--2021年11月25日的订单完成率为?
select sum(case when status='completed' then 1 else 0 end) /count(order_id)
from hll_t1
where order_dt = '2021-11-25';

/*
 订单完成率是极为重要的指标，基本反应了产品服务的根本交付质量是否达标，是否符合用户的需求和标准。
 所以该指标通常是需要持续每天关注的，若看到该指标下降：可以考虑几个方面：
 1）是否是周末？
 2）是否极端天气？
 3）取消方是谁？
 具体定位原因，然后和业务方、产品等反馈，解决优化问题
 */

--用车至少两次，且主动取消过至少1次的用车方有多少名？
select count(distinct user_id)
from hll_t1 a inner join(
select user_id
from hll_t1
group by user_id having count(user_id)>=2) b
inner join on a.user_id = b.user_id
where status = 'cancel_by_usr'
group by a.user_id having count(a.user_id)>=1;


--10月北京、上海的非禁止用车方的用车取消率分别为？
with not_banned as(
    SELECT usr_id
    FROM hll_t2
    where banned = 0
    and role = 'usr'
    )

select sum(case when status = 'cancel_by_usr' then 1 else 0 end) / count(order_id)
from hll_t1 a
inner join  not_banned b on a.usr_id = b.usr_id
where cty in ('北京','上海')
and order_dt >='2021-10-01'
and order_dt <'2021-11-01';
```

# 十二、物联网公司场景

例1.【小米】统计外电网各个设备的断电次数及断电时长，其中外电网正常状态为0，断电状态为1。统计断电次数，即是外电网的状态从0-1的变化次数。dt表示上报消息的时间，一分钟上报一次。

test.T1018数据格式：sid:设备ID - dt: 时间 (时间戳) - sta:外电网状态。

```sql
create table test.T1018 (
    sid int,
    dt string,
    sta int
);

insert into test.T1018 values
(1,"2021-06-18 10:11:12",0),
(1,"2021-06-18 10:12:10",1),
(1,"2021-06-18 10:13:00",1),
(1,"2021-06-18 10:14:01",1),
(1,"2021-06-18 10:15:00",1),
(1,"2021-06-18 10:16:02",0),
(1,"2021-06-18 10:17:01",1),
(1,"2021-06-18 10:18:00",1),
(1,"2021-06-18 10:19:03",1),
(1,"2021-06-18 10:20:04",0),
(2,"2021-06-18 10:11:13",0),
(2,"2021-06-18 10:12:00",0),
(2,"2021-06-18 10:13:00",1),
(2,"2021-06-18 10:14:00",1),
(2,"2021-06-18 10:15:00",0);

--1.统计断电次数，即是外电网的状态从0-1的变化次数
with temp as (

    select *,
           lag(sta,1,null) over(partition by sid order by dt) as next_sta,
           lag(dt,1,null) over(partition by sid order by dt) as next_dt
    from test.T1018
)

select sid,count(dt) as cnt_stop
from temp
where next_sta = 0 and sta = 1
group by sid
;

--求每次断电后断电时长,以及断电开始时间和结束时间
with temp as (

    select *,
           lag(sta,1,null) over(partition by sid order by dt) as next_sta,
           lag(dt,1,null) over(partition by sid order by dt) as next_dt
    from test.T1018
),
     temp1 as (

    select sid,dt,sta
    from temp
    where sta = 1 and next_sta = 0
    union all
    select sid,dt,sta
    from temp
    where sta = 0 and next_sta = 1
     ),
/*
或者
select sid,dt,sta
from temp
where (sta = 1 and next_sta = 0) or (sta = 0 and next_sta = 1);
*/
     temp2 as (

    select *,
           lag(sta,1,null) over(partition by sid order by dt) as next_staus,
           lag(dt,1,null) over(partition by sid order by dt) as next_time
    from temp1
     )

select sid,next_time as '断电开始时间',dt as '断电结束时间',
       unix_timestamp(dt) - unix_timestamp(next_time) as '断电持续时间'
from temp2
where sta = 0 and next_staus = 1;
```

例2.如下表所示：gw_id代表网关id,sensor_id代表设备id,avg_gd_vol代表每天的轨道电压均值点，compute_day代表日期

```sql
create table if not exists test.gd_vol(
            gw_id int,
            sensor_id string,
            avg_gd_vol float,
            compute_day string
        );
--gw_id,sensor_id,avg_gd_vol,compute_day
insert into test.gd_vol values
(1001,'123456',18.5,'2017-07-01'),
(1001,'123456',0.6,'2017-07-02'),
(1001,'123456',10.1,'2017-07-03'),
(1001,'123456',10.2,'2017-07-04'),
(1001,'123456',18.9,'2017-07-05'),
(1001,'123456',10.3,'2017-07-06'),
(1001,'123456',13.5,'2017-07-07'),
(1001,'234567',17.1,'2017-07-01'),
(1001,'234567',15.8,'2017-07-02'),
(1001,'234567',15.4,'2017-07-03'),
(1001,'234567',16.1,'2017-07-04'),
(1001,'234567',0.1,'2017-07-05'),
(1001,'234567',15.8,'2017-07-06'),
(1001,'234567',15.5,'2017-07-07');
```

现在要求以7天 为观察窗口，每天进行滑动计算7天内指标的异常值,异常趋势分析算法如下：

**计算滑动窗口（按1天滑动）内的点与其余各点之间的距离之和的平均值，作为特征指标**，例如第一条数据的`avg_gd_vol`是18.5,则整个滑动区间有7个数分别为[18.5,0.6,10.1,10.2,18.9,10.3,13.5]，所以计算方式为：
$$
(| 18.5 - 18.5 | + |18.5 - 0.6| + ...+|18.5 - 13.5|) /6
$$

```sql
with t as (
select gw_id
           ,sensor_id
           ,avg_gd_vol
           ,compute_day
           ,collect_list(avg_gd_vol) over(partition by gw_id,sensor_id) as avg_gd_vol_list
from test.gd_vol
)
select gw_id
,sensor_id
,compute_day
,cast(((abs(avg_gd_vol - avg_gd_vol_list[0]) + abs(avg_gd_vol - avg_gd_vol_list[1]) + 
      abs(avg_gd_vol - avg_gd_vol_list[2])+ abs(avg_gd_vol - avg_gd_vol_list[3])+ 
      abs(avg_gd_vol - avg_gd_vol_list[4])+ abs(avg_gd_vol - avg_gd_vol_list[5])+ 
      abs(avg_gd_vol - avg_gd_vol_list[6]))/6) as decimal(6,2))*100 as avg_d
from t 
;
```

这个写法有几个值得注意的点：

1. `collect_list() over(partition by ..) `窗口函数
2. `collect_list` 生成list后，就可以用切片了，如`avg_gd_vol_list[0]` 取第一个数
3. `cast(... as decimal(6,2))` 这种四舍五入会比`round`准一些

还有一种写法,用到了`lateral view explode()`

```sql
select gw_id
      ,sensor_id
      ,avg_gd_vol
      ,compute_day
      ,avg_d
from(
   select gw_id
        ,sensor_id
        ,avg_gd_vol
        ,compute_day
        ,cast((sum(abs(avg_gd_vol-avg_gd_vol_list_expl)) over(PARTITION by gw_id,sensor_id,compute_day) / (count(abs(avg_gd_vol-avg_gd_vol_list_expl)) over(PARTITION by gw_id,sensor_id,compute_day) -1))  as decimal(6,2))*100 as avg_d
        ,row_number() over(PARTITION by gw_id,sensor_id,compute_day order by compute_day) as rn
   from(
      select gw_id
        ,sensor_id
        ,avg_gd_vol
        ,collect_list(cast(avg_gd_vol as string)) over(PARTITION by gw_id,sensor_id) as avg_gd_vol_list
        ,compute_day
      from test.gd_vol 
      ) t lateral view explode(avg_gd_vol_list) tt as avg_gd_vol_list_expl
   ) m
where rn = 1;
```

# 十三、斗鱼直播平台场景

数据为主播ID,stt表示开播时间，edt表示下播时间。

```sql
create table if not exists test.play(
id int,
stt string,
edt string
);

insert into test.play values
(1001,'2021-06-14 12:12:12','2021-06-14 18:12:12'),
(1003,'2021-06-14 13:12:12','2021-06-14 16:12:12'),
(1004,'2021-06-14 13:15:12','2021-06-14 20:12:12'),
(1002,'2021-06-14 15:12:12','2021-06-14 16:12:12'),
(1005,'2021-06-14 15:18:12','2021-06-14 20:12:12'),
(1001,'2021-06-14 20:12:12','2021-06-14 23:12:12'),
(1006,'2021-06-14 21:12:12','2021-06-14 23:15:12'),
(1007,'2021-06-14 22:12:12','2021-06-14 23:10:12');
```

**1.该平台某一天主播同时在线人数最高为多少？**

分析思路：首先我们需要将这样一条记录进行拆分，分成不同的记录或数据流进入累加器，然后给每条记录进行标记，如果开播的话该条记录记为1，下播的话记为-1，此时的数据流按照时间顺序依次进入累加器，然后在累加器中进行叠加，其中累计的结果最大时候就是所求的结果。

**其实本质是利用累加器思想，但进入累加器的数据是按时间排好序的时序流数据（数据进入按时间先后顺序进入）。**

```sql
select max(cur_cnt)
from(
	select id
		 ,dt
		 ,sum(flag) over(order by dt) as cur_cnt
	from(
		select id,stt dt , 1 flag from play
		union
		select id,edt dt ,-1 flag from play
	) t
) m
```
**出现高峰时的时间段，也就是高峰时间的起始时间及结束时间，或持续时长**

```sql
with
t1 as (

     select id,stt dt , 1 flag from play
     union
     select id,edt dt ,-1 flag from play

),

t2 as (

    select id
           ,dt
           ,flag
           ,sum(flag) over(order by dt) as cur_cnt
           ,lead(dt,1,null) over(order by dt) as next_dt
    from t1
),

t3 as (

    select max(cur_cnt) as max_cur
    from t2

)

select  id
        ,dt as start_time
        ,next_dt as end_time
        ,unix_timestamp(next_dt) - unix_timestamp(dt) as duration
from t2
where cur_cnt = (select distinct max_cur from t3);
```

总结：

事实上该问题的分析在业务上具有重要的意义，我们能够实时跟踪随着时间变化的在线人数，了解服务器的负载变化情况，服务器的实时并发数等。该问题在不同业务场景下，有不同意义，比如某个游戏的同时在线人数，比如某个服务器的实时并发数，比如某个仓库的货物积压数量，某一段时间内的同时处于服务过程中的最大订单量等。实际上求最大在线人数和求实时在线人数是一回事，最大人数依赖于当前在线人数表，只有先求出当前在线人数表，才能求出最大同时在线人数。

# 十四、京东电商场景

如下为电商公司用户访问时间数据：test.t1027表

```
id	         ts - 访问时间戳
1001	17523641234
1001	17523641256
1002	17523641278
1001	17523641334
1002	17523641434
1001	17523641534
1001	17523641544
1002	17523641634
1001	17523641638
1001	17523641654
```

某个用户相邻的访问记录如果时间间隔小于 60 秒，则分为同一个组，结果为

```sql
id	         ts	     groupid
1001	17523641234	   1
1001	17523641256	   1
1001	17523641334	   2
1001	17523641534	   3
1001	17523641544    3
1001	17523641638	   4
1001	17523641654	   4
1002	17523641278	   1
1002	17523641434	   2
1002	17523641634	   3
```

```sql
with 
--这一步是在做数据类型处理；可忽略。重点看一下下面的逻辑：累加器
t as (
    select cast(id as int) as id,
           cast(ts as bigint) as ts
    from test.t1027
), 
     t1 as(
         select *,
       lag(ts,1,0) over(partition by id order by ts) as last_ts,
       ts -  lag(ts,1,0) over(partition by id order by ts) as diff
        from t
     ),

     t2 as (
         select *,
         case when diff > 60 then 1 else 0 end as flag
         from t1
     )

select id,
       ts,
       sum(flag) over(partition by id order by ts) as groupid
from t2
;
```

# 十五、米哈游游戏场景

 test.t1028记录了用户每日登陆数据：

```sql
create table if not exists test.t1028(
id int,
dt string);

insert into test.t1028 values
(1001,'2021-12-12'),
(1001,'2021-12-13'),
(1001,'2021-12-14'),
(1001,'2021-12-16'),
(1001,'2021-12-19'),
(1001,'2021-12-20'),
(1002,'2021-12-12'),
(1002,'2021-12-16'),
(1002,'2021-12-17');
```

 计算每个用户最大的连续登录天数.

这道题可以用第十点。计算连续天数的方法：

```sql
with t as (
   select *,
       to_date(date_sub(dt,row_number() over (partition by id order by dt))) as diff
from test.t1028
),
t1 as(
select id,count(diff) as cnt
from t
group by id,diff
    )
select id,max(cnt)
from t1
group by id
;
```

现在介绍另一种方法，重分组思想【使用hive实现】

```sql
with t as(
select *,
    datediff(dt,lag(dt,1,dt) over(partition by id order by dt)) rd
    from test.t1028
    ),
   
    t1 as(
    select *,
    sum(if(rd>1,1,0)) over(partition by id order by dt) as flag
    from t
    ),
    
    t2 as (
    
    select id,count(flag) as cnt
    from t1
    group by id,flag
    )
    
    select id,max(cnt)
    from t2
    group by id;
    
```

这样写有一个好处，如果不是连续一天，而是可以间隔2天、3天都算连续，那这个方法会比第一种好。

比如：计算每个用户最大的连续登录天数，可以间隔一天。**解释：如果一个用户在 1,3,5,6 登录游戏，则视为连续 6 天登录。**

```sql
with t as(
select *,
    datediff(dt,lag(dt,1,dt) over(partition by id order by dt)) rd
    from test.t1028
    ),
    
    t1 as(
    select *,
            sum(if(rd>2,1,0)) over(partition by id order by dt) as flag
    from t
    ),
    
    t2 as (
    
    select id,datediff(max(dt),min(dt))+1 as cnt
    from t1
    group by id,flag
    )
    
    select id,max(cnt)
    from t2
    group by id;
```

这个方法还是很好用的~

