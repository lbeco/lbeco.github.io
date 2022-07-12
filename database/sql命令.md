# sql命令

## 建表

```sql
CREATE TABLE `user_profile` (
`device_id` int NOT NULL,
`gender` varchar(14) NOT NULL,
`age` int ,
`university` varchar(32) NOT NULL,
`gpa` float,
`active_days_within_30` int
);
```

## 建索引

```sql
alter table 表名 add index 索引名 (column_list) ;
alter table 表名 add unique (column_list) ;
alter table 表名 add primary key (column_list) ;
```



## 去重选择

```sql
select distinct university from user_profile 
```



## like

```sql
select 
device_id,
age,
university
from user_profile where university like "北京%"
```



## 选择分数最高的学生

```sql
select  gpa from user_profile where university = "复旦大学" order by gpa desc limit 0,1
```



## 获取男生的平均GPA

```sql
select
  count(gender)  male_num, #count总数
  round(avg(gpa),1)  avg_gpa 
from user_profile GROUP BY gender having gender ='male'
```

count：总数

round：向下取整，保留x位小数

group by 分组，返回分组的字段，其他字段可以进行avg,count等操作，分组筛选靠having  

## 获取各个学校和年级的平均活跃天数和平均发帖数量

```sql
SELECT gender,university,COUNT(university) user_num,
AVG(active_days_within_30) avg_active_days,
AVG(question_cnt) avg_question_cnt
FROM user_profile
GROUP BY university,gender
```

## sql21

```sql
SELECT up.device_id,question_id,result
FROM user_profile up
JOIN question_practice_detail qpd
    ON up.device_id=qpd.device_id
WHERE university='浙江大学';
```

