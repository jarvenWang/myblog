---
author: "wangjinbao"
title: "SQL高级用法"
date: 2023-05-23
description: "几种好用的SQL高级用法。"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: wangjinbao
authorEmoji: 👻
tags:
- db
- categories:

---

## 1.自定义排序 (ORDER BY FIELD)
在MySQL中ORDER BY 排序除了 ASC 和 DESC 之外，还可以用自定义排序方式实现

```SQL
select * from movies ORDER BY FIELD(movie_name,'神话','警察故事'...)
```

## 2.空值NULL排序 (ORDER BY IF(ISNULL))
在MySQL中ORDER BY排序字段名，如果字段中存在NULL值就会对我们的排序结果造成影响。
这时候可用使用 ORDER BY IF(ISNULL(字段),0,1) 语法将NULL值转换成 0 或 1 ，实现NULL值数据排序到数据集前面或后面
```SQL
select * from movies ORDER BY actors,price desc;
select * from movies ORDER BY IF(ISNULL(actors),0,1),actors,price desc;
```

## 3.CASE表达式 (CASE...WHEN)
在开发中经常用if...else if...else，这时用CASE...WHEN表达式解决这个问题。
如：学生90分以上为优秀，分数80-90评为良好，分数60-80评为一般，分数低于60评为较差。查询方式如下：
```SQL
select * , case when score >90 then '优秀'
                when score >80 then '良好' 
                when score >60 then '一般'
                else '较差' end level
from student;
```

## 4.分组连接函数 (GROUP_CONCAT)
分组连接函数 可以在分组后指定字段的 <b><font color="cyan">字符串连接方式</font></b> ，并且还可以 <b><font color="cyan">指定排序逻辑</font></b> ；连接字符串默认为`英文逗号`。
```SQL
select actors,
GROUP_CONCAT(movie_name),
GROUP_CONCAT(price) from movies GROUP BY actors;

select actors,
GROUP_CONCAT(movie_name order by price desc SEPARATOR '_'),
GROUP_CONCAT(price order by price desc SEPARATOR '_') 
from movies GROUP BY actors;
```

## 5.分组统计数据后再进行统计汇总 (with rollup)

在mysql中用 `whth rollup`在分组统计数据的基础上再进行数据统计汇总，即将分组后的数据进行汇总。
```SQL
select actors,SUM(price) FROM movies GROUP BY actors;

select actors,SUM(price) FROM movies GROUP BY actors WITH ROLLUP ;
```

## 6.子查询提取 (with as)

如果一整句查询中多个子查询都需要使用同一个子查询的结果，就可以用`with as` 将共用的子查询提取出来并取一个别名。
例子：
获取演员刘亦菲票价大于50且小于65的数据。
```SQL
with m1 as (select * from movies where price>50),
 m2 as (select * from movies where price >=65)
select * from m1 where m1.id not in (select m2.id from m2)
 and m1.actors = '刘亦菲';
```
`
## 7.优雅处理数据插入、更新时主键、唯一键重复 
### 7.1 有则忽略，无则插入(IGNORE)
```SQL
select * from movies where id >= 13;

INSERT IGNORE INTO movies(id,movie_name,actors,price,release_date) VALUES
(13,'神话','成龙',100,'2005-12-22');
```
### 7.2 有则删除+插入，无则插入(REPLACE)
```SQL
REPLACE INSERT INTO movies(id,movie_name,actors,price,release_date) VALUES
(13,'神话','成龙',100,'2005-12-22');
```
### 7.1 有则更新，无则插入(on duplicate key update)
```SQL
INSERT INTO movies(id,movie_name,actors,price,release_date) VALUES
(13,'神话','成龙',100,'2005-12-22')
 on duplicate key update price = price +10;
```

