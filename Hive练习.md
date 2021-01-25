# Hive练习

https://www.cndba.cn/lirui/article/3391

# 数据准备

student.txt

```xml
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

