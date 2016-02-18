---
author: 邹政华
comments: true
date: 2015-04-20
published: true 
tags: 数据库
layout: post
title: sql优化实践 
categories: 编程 
---

1.索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率.一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有必要;

2.任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段;

3.嵌套一般不要超过3层，尽量使用联结替换嵌套查询;

4.对查询进行优化，应尽量避免全表扫描，首先应考虑在where及order by涉及的列上建立索引;

5.使用like进行模糊查询时应注意避免在关键词前加%
>select*from contact where username like ‘%story%’  
   关键词%story%，由于story前面用到了“%”，因此该查询必然走全表扫描，除非必要，否则不要在关键词前加%，

6.不要以字符格式声明数字，要以数字格式声明字符值。（日期同样）否则会使索引无效，产生全表扫描;

>SELECT emp.name, emp.job FROM emp WHERE emp.empno = 6789;
不要使用：SELECT emp.name, emp.job FROM emp WHERE emp.empno = ‘6789’

7.避免使用耗费资源的操作，带有DISTINCT,UNION,MINUS,INTERSECT,ORDER BY的SQL语句会启动SQL引擎 执行，耗费资源的排序(SORT)功能. DISTINCT需要一次排序操作, 而其他的至少需要执行两次排序;

8.避免在WHERE子句中使用in，not  in，or 或者having,
可以使用 exist 和not exist代替 in和not in;

9.在WHERE 语句中，尽量避免对索引字段进行计算操作,对索引字段进行计算操作可能会导致无法使用索引，降低效率

>......
where trunc(create_date)=trunc(:date1)
虽然已对create_date 字段建了索引，但由于加了TRUNC，使得索引无法用上。

