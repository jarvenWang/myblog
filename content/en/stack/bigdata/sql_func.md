---
author: "wangjinbao"
title: "SQL窗口函数"
date: 2023-05-24
description: "在大数据开发中，窗口函数是一种强大的功能，用于在查询结果中计算聚合、排序和排名等操作"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags:
- bigdata
- categories:

---
# 窗口函数
窗口函数表达式：
```SQL
function(args) OVER ([PARTITION BY expression] [ORDER BY expression [ASC|DESC]] [frame])

rank()over(partition by xxx 分组 order by xxx 排序)
```
## 1.排序函数
### 1.1 row_number()
序号不重复，序号连续
形如：`1,2,3...`

### 1.2 rank()
序号可以重复，序号不连续
形如：`1,2,2,4...`


### 1.3 dense_rank()
序号可以重复，序号连续
形如：`1,2,2,3...`

## 2.聚合函数
+ sum,avg,min,max...

```SQL
select name,deptno,salary,SUM(salary) over (partition by deptno)
 as total_cost from employees;
select name,deptno,salary,AVG(salary) over (partition by deptno)
 as total_cost from employees;
select name,deptno,salary,MIN(salary) over (partition by deptno)
 as total_cost from employees;
select name,deptno,salary,MAX(salary) over (partition by deptno)
 as total_cost from employees;
```
+ 加上 order by 会累积计算上一条数据结果
```SQL
select name,deptno,salary,SUM(salary) over (partition by deptno order by salary desc ) as total_cost from employees;

select name,deptno,salary,AVG(salary) over (partition by deptno order by salary desc ) as total_cost from employees;
```
+ 没有分区字段
```SQL
select name,deptno,salary,SUM(salary) over ()
 as total_cost from employees;
```
+ 分区字段可以为 多个
```SQL
select name,deptno,salary,SUM(salary) over (partition by deptno,salary)
  as total_cost from employees;
```

## 3.字段头部值、尾部值函数

first_value,last_value over partition by
```SQL
select name,
       deptno,
       hiredate,
first_value(hiredate) over (partition by deptno order by hiredate) as `first`,
last_value(hiredate) over (partition by deptno order by hiredate)  as `last`
from employees;
```

## 4.字段上一个、下一个值函数
lag, over partition by (lag上一个) 
`lag(col,n,default)` n 表上n个
lead, over partition by (lead下一个) 
`lead(col,n,default)` n 表下n个
```SQL
select name,
       deptno,
       hiredate,
       lead(hiredate) over (partition by deptno order by hiredate) as `lead`,
       lag(hiredate) over (partition by deptno order by hiredate)  as `lag`
from employees;
```