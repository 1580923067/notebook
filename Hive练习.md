# Hive练习

https://www.cndba.cn/lirui/article/3391

# 数据准备

student.txt

```xml
s_id  s_name  s_birth      s_sex
01    赵雷    1990-01-01    男
02    钱电    1990-12-21    男
03    孙风    1990-05-20    男
04    李云    1990-08-06    男
05    周梅    1991-12-01    女
06    吴兰    1992-03-01    女
07    郑竹    1989-07-01    女
08    王菊    1990-01-20    女

建表
create table if not exists student(
	s_id string,
	s_name string,
	s_birth string,
	s_sex string
)
row format delimited
fields terminated by '/t'
stored as textfile
```

course.txt

```xml
c_id c_name t_id
01 语文 02
02 数学 01
03 英语 03

建表
create table if not exists course(
	c_id string,
	c_name string,
	t_id string
)
row format delimited
fields terminated by '/t'
stored as textfile
```

teacher.txt

```xml
t_id t_name
01 张三
02 李四
03 王五

建表
create table if not exists teacher(
	t_id string,
	t_name string
)
row format delimited
fields terminated by '/t'
stored as textfile
```

score.txt

```xml
s_id    c_id    s_score
01      01      80
01      02      90
01      03      99
02      01      70
02      02      60
02      03      80
03      01      80
03      02      80
03      03      80
04      01      50
04      02      30
04      03      20
05      01      76
05      02      87
06      01      31
06      03      34
07      02      89
07      03      98

建表
create table if not exists score(
	s_id string,
	c_id string,
	s_score int
)
row format delimited
fields terminated by '/t'
stored as textfile
```

# 题目练习

## 查询“01”课程比“02”课程成绩高的学生的信息及课程分数

```
select a.*,b.s_score as 01_score,c.s_score as 02_score
from student a
join score b on a.s_id=b.s_id and b.c_id='01'
left join score c on a.s_id=c.s_id and c.c_id='02'
where b.s_score>c.s_score
```

## 查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```
select * from(
	select a.s_id,a.s_name,round(avg(b.s_score),2)) as avg_score
	from student a join score b on a.s_id=b.s_id
	group by a.s_id,a.s_name) t
where t.avg_score>=60

```

## 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

```
select a.s_id, a.s_name, count(b.c_id) num_subject,sum(b.s_score) total_score
from student a
join score b ob a.s_id=b.s_id
group by a.s_id,a.s_name
```

## 查询“李”姓老师的数量

```
select count(*) 
from teacher where t_name like '李%'
```

## 查询学过“张三”老师授课的同学的信息

```
select didtinct a.*
from student a
join score b on a.s_id=b.s_id
left join course c on b.c_id=c.c_id
left join teacher d on c.t_id=d.t_id
where d.t_name='张三'
```

## 查询学过编号为“01”并且也学过编号为“02”的课程的同学的信息

```
select *
from student a
join score b on a.s_id=b.s_id and b.c_id='01'
join score c on a.s_id=c.s_id and c.c_id='02'
```

## 查询没有学全所有课程的同学的信息

```
select distinct a.*
from student a
join score b
left join score c on a.s_id=c.s_id and b.c_id=c.c_id
where c.s_score is null
```

## 查询至少有一门课与学号为“01”的同学序偶学相同的同学的信息

# 12道SQL

## 第1题

我们有如下的用户访问数据

| userId | visitDate | visitCount |
| ------ | --------- | ---------- |
| u01    | 2017/1/21 | 5          |
| u02    | 2017/1/23 | 6          |
| u03    | 2017/1/22 | 8          |
| u04    | 2017/1/20 | 3          |
| u01    | 2017/1/23 | 6          |
| u01    | 2017/2/21 | 8          |
| u02    | 2017/1/23 | 6          |
| u01    | 2017/2/22 | 4          |

要求使用SQL统计出每个用户的累积访问次数，如下表所示：

| 用户id | 月份    | 小计 | 累积 |
| ------ | ------- | ---- | ---- |
| u01    | 2017-01 | 11   | 11   |
| u01    | 2017-02 | 12   | 23   |
| u02    | 2017-01 | 12   | 12   |
| u03    | 2017-01 | 8    | 8    |
| u04    | 2017-01 | 3    | 3    |

数据：

u01   2017/1/21    5

u02   2017/1/23    6

u03   2017/1/22    8

u04   2017/1/20    3

u01   2017/1/23    6

u01   2017/2/21    8

u02   2017/1/23    6

u01   2017/2/22    4

1. 建表

   `create table first(userId string,visitDate String,visitCount string)`

2. 插入数据

   ```
   insert into table first values('u01','2017/1/21','5');
   insert into table first values('u02','2017/1/23','6');
   insert into table first values('u03','2017/1/22','8');
   insert into table first values('u04','2017/1/20','3');
   insert into table first values('u01','2017/1/23','6');
   insert into table first values('u01','2017/2/21','8');
   insert into table first values('u02','2017/1/23','6');
   insert into table first values('u01','2017/2/22','4');
   
   select 
   date_format(visitDate,'yyyy-MM') aa
   from first
   group by aa;
   ```

3. 转变时间格式

   ```
   select from_unixtime(unix_timestamp(visitDate,'yyyy/mm/dd'),'yyyy-mm');
   ```

   

4. 需求分析

   ```
   1、格式化时间
   select
   	userId,
   	from_unixtime(unix_timestamp(visitDate,'yyyy/mm/dd'),'yyyy-mm') month,
   	visitCount
   from first;         ------t1表
   
   2、求月份的总次数
   select
   	userId,
   	month,
   	sum(vistitCount) sum_month
   from t1
   group by userId,month;     -------t2表
   
   3、按照用户id开窗，按照时间排序
   select
   	userId,
   	month,
   	sum_month
   	sum(sum_month) over (partition by userId order by month rows between UNBOUNDED 	PERCEDING and current row)sum_all
   from t2
   
   4、终极sql
   select
   	t2.userId,
   	t2.month,
   	sum_month
   	sum(t2.sum_month) over (partition by userId order by month rows between UNBOUNDED 	PERCEDING and current row)sum_all
   from
   (	select
   		t1.userId,
   		t1.month,
   		sum(t1.vistitCount) sum_month
   	from 
   	(
   		select
   			userId,
   			from_unixtime(unix_timestamp(visitDate,'yyyy/mm/dd'),'yyyy-mm') month,
   			visitCount
   		from first; 
   	)t1
   	group by userId,month)t2;
   	
   ```

## 第2题

有50W个京东店铺，每个顾客访客访问任何一个店铺的任何一个商品时都会产生一条访问日志，访问日志存储的表名为Visit，访客的用户id为user_id，被访问的店铺名称为shop，请统计：

u1 a

u2 b
u1 b
u1 a
u3 c
u4 b
u1 a
u2 c
u5 b
u4 b
u6 c
u2 c
u1 b
u2 a
u2 a
u3 a

建表：

```
create table visit(user_id string,shop string) row format delimited fields terminated by '\t';
```

1、每个店铺的UV（访客数）

```
select shop,count(distinct user_id) from visit group by shop;
```

2、每个店铺访问top3的访客信息。输出店铺名、访客id、访问次数

2.1 查询每个店铺被每个用户访问次数

```
select shop,user_id,count(*) ct
from visit
group by shop,user_id;t1
```

2.2 计算每个店铺被用户访问次数排名

```
select shop,user_id,ct,rank() over(partition by shop order by ct) rk
from t1;t2
```

2.3 取每个店铺排名前3的

```
select shop,user_id,ct
from t2
where rk<=3;
```

2.4 最终sql

```
select shop,user_id,ct
from
	(select 
		shop,
		user_id,
		ct,
		rank() over(partition by shop order by ct) rk
	from 
		(select 
			shop,
			user_id,
			count(*) ct
		from visit
		group by shop,user_id)t1
	)t2
where rk<=3;
```

## 第3题

已知一个表STG.ORDER，有如下字段:Date，Order_id，User_id，amount。请给出sql进行统计:数据样例:                               2017-01-01,10029028,1000003251,33.57。

建表：

```
create table order_tab(dt string,order_id string,user_id string,amount decimal(10,2)) row format delimited fields terminated by '\t';
```

1、给出2017年每个月的订单数、用户数、总成交金额

```
select 
	date_format(dt,'yyyy-MM'),
	count(Order_id),
	count(distinct User_id),
	sum(amount)
from order_tab 
group by date_format(dt,'yyyy-MM');
```

2、给出2017年11月的新客数（指在11月才有第一笔订单）

```
select
	count(user_id)
from
	order_tab
group by user_id
having date_format(min(dt),'yyyy-MM')='2017-11';
```

## 第4题

有一个5000万的用户文件(user_id，name，age)，一个2亿记录的用户看电影的记录文件(user_id，url)，根据年龄段观看电影的次数进行排序？

建表：

```
create table forth_user(user_id string,name string,age int);
create table forth_log(user_id string,url string);
```

分析需求：

先求出每个人看了几次电影，t1

然后t1和user表join，拼接age字段，t2

划分年龄段，0-20,20-40,40-60,60--

按照年龄段分组，按照次数排序

```
select
	user_id,
	count(*) con
from forth_log
group by user_id;   -----t1


select
	u1.age age,
	t1.user_id,
	t1.con con
from forth_user u1
join
(
	select
		user_id,
		count(*) con
	from forth_log
	group by user_id
)t1
on u1.user_id=t1.user_id    -----t2


select
	t2.con con,
	case
		when 0<=t2.age and t2.age<20 then 'a'
		when 0<=t2.age and t2.age<20 then 'a'
        when 20<=t2.age and t2.age<40 then 'b' 
        when 40<=t2.age and t2.age<60 then 'c' 
        else 'd'
     end as category
from t2   -------t3

select 
    t3.category 
    sum(t3.con)
from t3
gruop by t3.category 

```

终极sql

```
select
    t4.category,
    t4.sumcon
from 

(
select 
    t3.category category,
    sum(t3.con) sumcon
from 
(
    select 
        t2.con con,
        case 
            when  0<=t2.age  and t2.age<20 then 'a'
            when  20<=t2.age and t2.age<40 then 'b'
            when  40<=t2.age and t2.age<60 then 'c'
            else    'd'
        end as category
    from 
    (
        select
            u1.age,
            t1.user_id,
            t1.con con
        from forth_user u1
        join 
        (
        select 
            user_id,
            count(*)    con
        from forth_log
        group by user_id
        )t1
        on u1.user_id=t1.user_id
    )t2 

)t3
group by t3.category 
)t4
order by t4.sumcon
```

## 第5题

有日志如下，请写出代码求得所有用户和活跃用户的总数及平均年龄。（活跃用户指连续两天都有访问记录的用户）

日期 用户 年龄

11,test_1,23

11,test_2,19

11,test_3,39

11,test_1,23

11,test_3,39

11,test_1,23

12,test_2,19

13,test_1,23

```
create table fiveth(`date` string,user_id string ,age int );
```

1、总人数和平均年龄

```
select
	count(*)
	avg(age)
from
(
	select
		user_id,age
	from fiveth
	group by user_id,age
)t1
```

2、活跃人数和平均年龄

- 先按照日期和用户去重
- 开窗函数，利用等差数列

```
select 
	user_id,'date',age
from
fiveth group by
user_id,'date',age   -----fiveth1

select
	'date',
	user_id,
	age,
	rank() over(partition by user_id order by 'date')rank
from
fiveth1            -------t1


select
	t1.'date'-rank 'date-dif'
	user_id,
	age
from t1          ----------t2


select
	t2.age,
	t2.user_id
from t2
group by t2.user_id,t2.'date_fid',t2.age
having count(*)>=2          -----------t3


select
	count(*)
	avg(t3.age)
from t3




终极sql


select 
    count(*),
    avg(t3.age)
from 
(
    select 
        t2.age,
        t2.user_id
    from 
    (
        select 
            t1.`date`- rank `date_dif`,
            user_id,
            age
        from 
        (
            select 
            `date`,
            user_id,
            age,
            rank() over(partition by user_id order by `date`) rank
            from 
            (
                select 
                    user_id,`date`,age

                from 
                fiveth
                group by 
                user_id,`date`,age

                )fiveth1
        )t1

    )t2
    group by t2.user_id,t2.`date_dif`,t2.age
    having count(*) >=2
)t3







```



## 第6题

请用sql写出所有用户中在今年10月份第一次购买商品的金额，表ordertable字段（购买用户：userid，金额：money，购买时间：paymenttime(格式：2017-10-01)，订单id：orderid）

建表：

```
create EXTERNAL table ordertable
(
 userid string,paymenttime string,
 money int,
 orderid string
)
row format delimited fields terminated by ',';
```

```
selelct 
	userid,
	money,
	paymenttime,
	orderid,
	rank() over(partition by userid order by orderid) rk
from ordertable
where date_format(paymenttime,'yyyy-MM')='2017-10';t1

select
	userid,
	money,
	paymenttime,
	orderid
from 
(selelct 
	userid,
	money,
	paymenttime,
	orderid,
	rank() over(partition by userid order by orderid) rk
from ordertable
where date_format(paymenttime,'yyyy-MM')='2017-10'
)t1
where rk=1;
```



## 第7题

现有图书管理数据库的三个数据模型如下：

图书（数据表名：BOOK）

| 序号 | 字段名称  | 字段描述 | 字段类型                |
| ---- | --------- | -------- | ----------------------- |
| 1    | BOOK_ID   | 总编号   | 文本                    |
| 2    | SORT      | 分类号   | 文本                    |
| 3    | BOOK_NAME | 书名     | 文本                    |
| 4    | WRITER    | 作者     | 文本                    |
| 5    | OUTPUT    | 出版单位 | 文本                    |
| 6    | PRICE     | 单价     | 数值（保留小数点后2位） |

读者（数据表名：READER）

| 序号 | 字段名称  | 字段描述 | 字段类型 |
| ---- | --------- | -------- | -------- |
| 1    | READER_ID | 借书证号 | 文本     |
| 2    | COMPANY   | 单位     | 文本     |
| 3    | NAME      | 姓名     | 文本     |
| 4    | SEX       | 性别     | 文本     |
| 5    | GRADE     | 职称     | 文本     |
| 6    | ADDR      | 地址     | 文本     |

借阅记录（数据表名：BORROW_LOG）

| 序号 | 字段名称   | 字段描述 | 字段类型 |
| ---- | ---------- | -------- | -------- |
| 1    | READER_ID  | 借书证号 | 文本     |
| 2    | BOOK_ID    | 总编号   | 文本     |
| 3    | BORROW_ATE | 借书日期 | 日期     |



（1）创建图书管理库的图书、读者和借阅三个基本表的表结构。请写出建表语句。

- 创建图书表

  ```
  create external table book
  (
  	book_id string comment '总编号',
  	sort string comment '分类号'，
  	book_name string comment '书名'，
  	writer string comment '作者'，
  	'output' string comment '出版单位'，
  	price decimal(10,2) comment '单价'
  )
  row format delimited fields terminated by '\\|'
  partitioned by (months string,day string);
  ```

  

- 常见读者表

  ```
  CREATE EXTERNAL table reader
  (
  reader_id string comment '借书证号',
  company string comment '单位',
  name string comment '姓名',
  sex string comment '性别',
  grade string comment '职称',
  addr string comment '地址'
  )
  ROW format delimited fields terminated by '\t';
  ```

  

- 创建借阅表

  ```
  create external table borrow_log
  (reader_id string comment '借书证号',
   book_id string comment '总编号',
   borrow_date date comment '结束日期'
  )
  row format delimited fields terminated by '\t';
  ```

（2）找出姓李的读者姓名（NAME）和所在单位（COMPANY）。

```
select
	name,
	company
from reader
where name like '李%';
	
```

（3）查找“高等教育出版社”的所有图书名称（BOOK_NAME）及单价（PRICE），结果按单价降序排序。

```
select
	book_name,
	price
from book
where 'output'='高等教育出版社'
order by price desc;
```

（4）查找价格介于10元和20元之间的图书种类(SORT）出版单位（OUTPUT）和单价（PRICE），结果按出版单位（OUTPUT）和单价（PRICE）升序排序。

```
select
	sort,
	'output',
	price,
fron book
where price>=10 and price<=20
order by 'output' asc,price asc;
```

（5）查找所有借了书的读者的姓名（NAME）及所在单位（COMPANY）。

```
select
	r.name,
	r.company,
from reader r join borrow_log b on r.reader_id=b.reader_id;
```

（6）求”科学出版社”图书的最高单价、最低单价、平均单价。

```
select
	max(price),
	min(price),
	avg(price)
from book
where 'output'='科学出版社';
```

（7）找出当前至少借阅了2本图书（大于等于2本）的读者姓名及其所在单位。

```
select
	reader_id,
from borrow_log
group by reader_id
having count(*)>=2;t1

select
	name,
	company
from reader r join
(
	select
		reader_id,
	from borrow_log
	group by reader_id
	having count(*)>=2
)t1 on r.reader_id=t1.reader_id;
```

（8）考虑到数据安全的需要，需定时将“借阅记录”中数据进行备份，请使用一条SQL语句，在备份用户bak下创建与“借阅记录”表结构完全一致的数据表BORROW_LOG_BAK.井且将“借阅记录”中现有数据全部复制到BORROW_L0G_ BAK中。

```
create external table borrow_log_bak like borrow_log;
insert into borrow_log_bak select * from borrow_log;
```

（9）现在需要将原Oracle数据库中数据迁移至Hive仓库，请写出“图书”在Hive中的建表语句（Hive实现，提示：列分隔符|；数据表数据需要外部导入：分区分别以month＿part、day＿part 命名）

```
create external table book_h(
	BOOK_ID string ,
	SORT string,
	BOOK_string,
	WRITER string,
	OUTPUT string ,
	PRICE int
)
raw format delimited feilds terminated by '|'
partitioned by (month＿part string ,day＿part string)
```

（10）Hive中有表A，现在需要将表A的月分区　201505　中　user＿id为20000的user＿dinner字段更新为bonc8920，其他用户user＿dinner字段数据不变，请列出更新的方法步骤。（Hive实现，提示：Hlive中无update语法，请通过其他办法进行数据更新）

```
create table A1 
as select * from A
where user_id <> 20000;
insert into A1 partition(month＿part =201505) user＿dinner value bonc8920 where user_id =20000;

insert overwrite table A 
select * from A1;
```



## 第8题

有一个线上服务器(server)访问日志格式如下（用sql答题）

时间           接口             ip地址

2016-11-09 11：22：05  /api/user/login         110.23.5.33

2016-11-09 11：23：10  /api/user/detail         57.3.2.16

.....

2016-11-09 23：59：40  /api/user/login         200.6.5.166

求11月9号下午14点（14-15点），访问api/user/login接口的top10的ip地址

```
create external table dws_active_web
(dt string,
interface string,
ip string)
row format delimited fields terminated by '\t';

insert into table dws_active_web values
('2016-11-09 14:22:05','/api/user/login','110.23.5.33'),
('2016-11-09 14:23:10','/api/user/detail','57.3.2.16'),
('2016-11-09 15:59:40','/api/user/login','200.6.5.166'),
('2016-11-09 14:28:35','/api/user/login','130.23.5.33'),
('2016-11-09 14:50:30','/api/user/detail','527.3.2.16'),
('2016-11-09 15:59:40','/api/user/login','203.6.5.166'),
('2016-11-09 13:22:05','/api/user/login','140.23.5.33'),
('2016-11-09 14:23:10','/api/user/detail','557.3.2.16'),
('2016-11-09 14:59:40','/api/user/login','230.6.5.166');
	
```

```
select
	ip,
	count(*) counts
from dws_active_web
where date_format(dt,'yyyy-MM-dd HH:mm:ss')>='2016-11-06 14:00:00' and  date_format(dt,'yyyy-MM-dd HH:mm:ss')<='2016-11-06 15:00:00' and interface='api/user/login'
group by ip
order by counts desc
limit 10;
```



## 第9题

有一个充值日志表如下：

CREATE TABLE `credit log` 

(

  `dist_id` int（11）DEFAULT NULL COMMENT '区组id',

  `account` varchar（100）DEFAULT NULL COMMENT '账号',

  `money` int(11) DEFAULT NULL COMMENT '充值金额',

  `create_time` datetime DEFAULT NULL COMMENT '订单时间'

)ENGINE=InnoDB DEFAUILT CHARSET-utf8

请写出SQL语句，查询充值日志表2015年7月9号每个区组下充值额最大的账号，要求结果：

区组id，账号，金额，充值时间

```
--测试数据
drop table if exists credit_log;
CREATE TABLE `credit_log` (
`dist_id` int ,
`account` string ,
`money` double ,
`create_time` string );

insert into credit_log values (1,'1232143224',26343.5,'2015-07-09');
insert into credit_log values (1,'1232143234',23423.5,'2015-07-09');
insert into credit_log values (1,'1232143224',23423.5,'2015-07-09');
insert into credit_log values (2,'1232143254',23743.5,'2015-07-09');
insert into credit_log values (2,'1232143264',23343.5,'2015-07-09');
insert into credit_log values (2,'1232143724',23543.5,'2015-07-09');
insert into credit_log values (3,'1232143274',23435.5,'2015-07-09');
insert into credit_log values (3,'1232143274',23443.5,'2015-07-09');
insert into credit_log values (3,'1232174324',22343.5,'2015-07-09');
---------------------------------------------------------------------------

select
	*
from 
(
	select 
		*,
	rank() over(partition by dist_id order by money desc) rank
	from credit_log
	where create_time='2015-07-09' 
)temp
where rank=1;
```



## 第10题

有一个账号表如下，请写出SQL语句，查询各自区组的money排名前十的账号（分组取前10）

CREATE TABIE `account` 

(

  `dist_id` int（11）

  DEFAULT NULL COMMENT '区组id'，

  `account` varchar（100）DEFAULT NULL COMMENT '账号' ,

  `gold` int（11）DEFAULT NULL COMMENT '金币' 

  PRIMARY KEY （`dist_id`，`account_id`），

）ENGINE=InnoDB DEFAULT CHARSET-utf8

 

重点：如何在mysql中实现类似oracle中rank() over()开窗函数的效果？

```
select
	*
from(
	select
		*,
	rank()over(partition by dist_id order by money desc) rank
	from credit_log
	where create_time='2015-07-09')temp
where rank<=10;
```



## 第11题

1）有三张表分别为会员表（member）销售表（sale）退货表（regoods）

（1）会员表有字段memberid（会员id，主键）credits（积分）；

（2）销售表有字段memberid（会员id，外键）购买金额（MNAccount）；

（3）退货表中有字段memberid（会员id，外键）退货金额（RMNAccount）；

2）业务说明：

（1）销售表中的销售记录可以是会员购买，也可是非会员购买。（即销售表中的memberid可以为空）

（2）销售表中的一个会员可以有多条购买记录

（3）退货表中的退货记录可以是会员，也可是非会员

（4）一个会员可以有一条或多条退货记录

查询需求：分组查出销售表中所有会员购买金额，同时分组查出退货表中所有会员的退货金额，把会员id相同的购买金额-退款金额得到的结果更新到表会员表中对应会员的积分字段（credits）

```
 --测试数据
create table member(
memberid int,
credits double
);

create table sale(
memberid int, 
MNAccount double
);

create table regoods(
memberid int,
RMNAccount double 
);
```

```
select 
	s.memberid memberid,
	s.mnaccount mnaccount
	r.rmnaccount rmnaccount
from sale s
join regoods r
on s.memberid=r.memberid  ---------t1


select 
	t1.memberid,
	sum(t1.mnaccount) sum_buy,
	sum(t1.rnaccount) sum_tui,
	sum_buy-sum_tui sum_dif
from t1
group by t1.memberid

insert into member select t2.memberid,t2.sum_dif from t2;
```



## 第12题

现在有三个表student（学生表）、course(课程表)、score（成绩单），结构如下：

create table student

(

  id bigint comment ‘学号’，

  name string comment ‘姓名’,

  age bigint comment ‘年龄’

);

create table course

(

  cid string comment ‘课程号，001/002格式’,

  cname string comment ‘课程名’

);

Create table score

(

  Id bigint comment ‘学号’,

  cid string comment ‘课程号’,

  score bigint comment ‘成绩’

) partitioned by(event_day string)

 

其中score中的id、cid，分别是student、course中对应的列请根据上面的表结构，回答下面的问题

1）请将本地文件（/home/users/test/20190301.csv）文件，加载到分区表score的20190301分区中，并覆盖之前的数据

```
load local data input '/home/users/test/20190301.csv' overwrite table score partition (event_day='20190301');
```

2）查出平均成绩大于60分的学生的姓名、年龄、平均成绩

```
select 
	id,
	avg(score) avg_score,
from score
group by id
having avg(score)>=60;  t1

select
	name,
	age,
	avg_score
from
(
	select 
		id,
		avg(score) avg_score,
	from score
	group by id
	having avg(score)>=60
)t1 join student s on t1.id=s.id;
```

3）查出没有‘001’课程成绩的学生的姓名、年龄

```
select
	id
from score
where cid='001';  t1

select 
	s1.id
	st.name
	st.age
from score s1 join student st on s1.id=st.id
where s1.id not in(select
	id
from score
where cid='001');
```

4）查出有‘001’\’002’这两门课程下，成绩排名前3的学生的姓名、年龄

```
select id 
from score s
where s.cid='001' and s.cid='002'; t1

select s1.id,s1.cid,s1.score,rank() over(partition by s1.cid order by s1.score desc) rk
from score s1 join (select id 
from score s
where s.cid='001' and s.cid='002')t1 on s1.id = t1.id; t2

select t2.id, st.name, st.age
from (select s1.id,s1.cid,s1.score,rank() over(partition by s1.cid order by s1.score desc) rk
from score s1 join (select id 
from score s
where s.cid='001' and s.cid='002')t1 on s1.id = t1.id)t2 join student st on t2.id =st.id
where rk <=3;
```

5）创建新的表score_20190317，并存入score表中20190317分区的数据

```
create external table score_20190317 
select * from score where event_day='20190317';
```

6）如果上面的score表中，uid存在数据倾斜，请进行优化，查出在20190101-20190317中，学生的姓名、年龄、课程、课程的平均成绩

```
1．	distribute by的分区规则是根据分区字段的hash码与reduce的个数进行模除后，余数相同的分到一个区。
2．	Hive要求DISTRIBUTE BY语句要写在SORT BY语句之前 

select id,collect_set(cname) cnames,avg(score) avg_score
from score s1 join course c on s1.cid=c.cid
group by s1.id; t1
--关联student表
select name,age,cnames,avg_score
from student s join s
(
select id,collect_set(cname) cnames,avg(score) avg_score
from score s1 join course c on s1.cid=c.cid
group by s1.id
)t1  on s.id=t1.id;
```

7）描述一下union和union all的区别，以及在mysql和HQL中用法的不同之处？

```
UNION用的比较多union all是直接连接，取到得是所有值，记录可能有重复   union 是取唯一值，记录没有重复   
1、UNION 的语法如下：
     [SQL 语句 1]
      UNION
     [SQL 语句 2]

2、UNION ALL 的语法如下：
     [SQL 语句 1]
      UNION ALL
     [SQL 语句 2]

效率：
UNION和UNION ALL关键字都是将两个结果集合并为一个，但这两者从使用和效率上来说都有所不同。
1、对重复结果的处理：UNION在进行表链接后会筛选掉重复的记录，Union All不会去除重复记录。
2、对排序的处理：Union将会按照字段的顺序进行排序；UNION ALL只是简单的将两个结果合并后就返回。
从效率上说，UNION ALL 要比UNION快很多，所以，如果可以确认合并的两个结果集中不包含重复数据且不需要排序时的话，那么就使用UNION ALL。
```

8）简单描述一下lateral view语法在HQL中的应用场景，并写一个HQL实例

# 课件中的练习题

## case when

| name | dept_id | sex  |
| ---- | ------- | ---- |
| 悟空 | A       | 男   |
| 大海 | A       | 男   |
| 宋宋 | B       | 男   |
| 凤姐 | A       | 女   |
| 婷姐 | B       | 女   |
| 婷婷 | B       | 女   |

求出不同部门男女各多少人？

A   2    1

B   1    2

```
select 
	dept_id,
	sum(case sex when '男' then 1 else 0 end) male_count,
	sum(case sex when '女' then 1 else 0 end) female_count
from 
	emp_sex
group by dept_id;
```

## 行转列

CONCAT(string A/col, string B/col…)：返回输入字符串连接后的结果，支持任意个输入字符串;

CONCAT_WS(separator, str1, str2,...)：它是一个特殊形式的 CONCAT()。第一个参数剩余参数间的分隔符。分隔符可以是与剩余参数一样的字符串。如果分隔符是 NULL，返回值也将为 NULL。这个函数会跳过分隔符参数后的任何 NULL 和空字符串。分隔符将被加到被连接的字符串之间;

COLLECT_SET(col)：函数只接受基本数据类型，它的主要作用是将某字段的值进行去重汇总，产生array类型字段。

| name   | constellation | blood_type |
| ------ | ------------- | ---------- |
| 孙悟空 | 白羊座        | A          |
| 大海   | 射手座        | A          |
| 宋宋   | 白羊座        | B          |
| 猪八戒 | 白羊座        | A          |
| 凤姐   | 射手座        | A          |
| 苍老师 | 白羊座        | B          |

需求：

把星座和血型一样的人归类到一起。结果如下：

射手座,A      大海|凤姐

白羊座,A      孙悟空|猪八戒

白羊座,B       宋宋|苍老师

```
select
	t1.base,
 	concat_ws("|",collect_set(t1.name)) name
from
	(select
		name,
		concat(constellation,",",blood_type) base
	 from
	 	person_info)t1
group by t1.base;
```

## 列转行

EXPLODE(col)：将hive一列中复杂的array或者map结构拆分成多行。

LATERAL VIEW
		用法：LATERAL VIEW udtf(expression) tableAlias AS columnAlias
		解释：用于和split, explode等UDTF一起使用，它能够将一列数据拆成多行数据，在此基础上可以对拆分后的数据进行聚合。

Lateral view explode()   临时表名   as  临时列名

处理：
    	①先explode
		②需要将炸裂后的1列N行，在逻辑上依然视作1列1行，实际是1列N行，和movie进行笛卡尔集
		这个操作在hive中称为侧写(lateral vIEW)
		

		Lateral view explode()   临时表名   as  临时列名

| movie         | category                 |
| ------------- | ------------------------ |
| 《疑犯追踪》  | 悬疑,动作,科幻,剧情      |
| 《Lie to me》 | 悬疑,警匪,动作,心理,剧情 |
| 《战狼2》     | 战争,动作,灾难           |

将电影分类中的数组数据展开。结果如下：

《疑犯追踪》   悬疑

《疑犯追踪》   动作

《疑犯追踪》   科幻

《疑犯追踪》   剧情

《Lie to me》  悬疑

《Lie to me》  警匪

《Lie to me》  动作

《Lie to me》  心理

《Lie to me》  剧情

《战狼2》     战争

《战狼2》     动作

《战狼2》     灾难

```
select 
	m.move,
	tb1.cate
from
	movie_info m
lateral view
	explode(split(category,",")) tb1 as cate;
```

## 窗口函数

OVER()：指定分析函数工作的数据窗口大小，这个数据窗口大小可能会随着行的变而变化。

CURRENT ROW：当前行

n PRECEDING：往前n行数据

n FOLLOWING：往后n行数据

UNBOUNDED：起点，UNBOUNDED PRECEDING 表示从前面的起点， UNBOUNDED FOLLOWING表示到后面的终点

LAG(col,n,default_val)：往前第n行数据

LEAD(col,n, default_val)：往后第n行数据

NTILE(n)：把有序窗口的行分发到指定数据的组中，各个组有编号，编号从1开始，对于每一行，NTILE返回此行所属的组的编号。注意：n必须为int类型。

**数据准备：name，orderdate，cost**

jack,2017-01-01,10

tony,2017-01-02,15

jack,2017-02-03,23

tony,2017-01-04,29

jack,2017-01-05,46

jack,2017-04-06,42

tony,2017-01-07,50

jack,2017-01-08,55

mart,2017-04-08,62

mart,2017-04-09,68

neil,2017-05-10,12

mart,2017-04-11,75

neil,2017-06-12,80

mart,2017-04-13,94

**需求**

（1）查询在2017年4月份购买过的顾客及总人数

```
select
	name,
	count(*) over()
from business
where substring(orderdate,1,7)='2017-04'
group by name;
```

（2）查询顾客的购买明细及月购买总额

（3）上述的场景, 将每个顾客的cost按照日期进行累加

（4）查询每个顾客上次的购买时间

（5）查询前20%时间的订单信息