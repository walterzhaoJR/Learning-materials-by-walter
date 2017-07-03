#2.11 SELECT语句
##通配符
* 关键字 LIKE 在SQL语句中和通配符一起使用，通配符代表未知字符。SQL中的通配符是 \_ 和 % 。其中 _ 代表一个未指定字符，% 代表不定个未指定字符。
比如，要只记得电话号码前四位数为1101，而后两位忘记了，则可以用两个 _ 通配符代替：
```linux
select name,age,phone from employee where phone like '1101__';
+------+------+--------+
| name | age  | phone  |
+------+------+--------+
| Joe  |   31 | 110129 |
| Mike |   23 | 110110 |
+------+------+--------+
2 rows in set (0.01 sec)
mysql> select name,age,phone from employee where phone like 'J%';
Empty set (0.00 sec)
mysql> select name,age,phone from employee where name like 'J%';
+------+------+--------+
| name | age  | phone  |
+------+------+--------+
| Jack |   24 | 120120 |
| Jim  |   35 | 100861 |
| Joe  |   31 | 110129 |
| Jobs | NULL |  19283 |
+------+------+--------+
4 rows in set (0.00 sec)
```

##查询结果排序
* 为了使查询结果看起来更顺眼，我们可能需要对结果按某一列来排序，这就要用到 ORDER BY 排序关键词。默认情况下，ORDER BY的结果是升序排列，而使用关键词ASC和DESC可指定升序或降序排序。
```linux
mysql> select name,age,salary,phone from employee order by salary desc;
针对salary降序排列：
+------+------+--------+--------+
| name | age  | salary | phone  |
+------+------+--------+--------+
| Joe  |   31 |   3600 | 110129 |
| Jobs | NULL |   3600 |  19283 |
| Ken  |   27 |   3500 | 654321 |
| Rick |   24 |   3500 | 987654 |
| Mike |   23 |   3400 | 110110 |
| Tony | NULL |   3400 | 102938 |
| Jim  |   35 |   3000 | 100861 |
| Mary |   21 |   3000 | 100101 |
| Alex |   26 |   3000 | 123456 |
| Rose |   22 |   2800 | 114114 |
| Tom  |   26 |   2500 | 119119 |
| Jack |   24 |   2500 | 120120 |
+------+------+--------+--------+
12 rows in set (0.00 sec)
```

##SQL内置函数和计算
* SQL 允许对表中的数据进行计算。对此，SQL 有 5 个内置函数，这些函数都对 SELECT 的结果做操作：
|函数名|COUNT|SUM|AVG|MAX|MIN|
|--|--|
|作用|计数|求和|求平均值|求最大值|求最小值|
```linux
比如计算出salary的最大、最小值，用这样的一条语句：
mysql> select max(salary) as max_salary,min(salary) as min_salary from employee;
+------------+------------+
| max_salary | min_salary |
+------------+------------+
|       3600 |       2500 |
+------------+------------+
1 row in set (0.00 sec)
其中as为查询结果起一个别名。
```

##子查询
* 然而有时必须处理多个表才能获得所需的信息，这样的查询就需要涉及到子查询。也可以说子查询就是嵌套查询。
* 子查询还可以扩展到三层或者多层。

```linux
SELECT of_dpt,COUNT(proj_name) AS count_project FROM project
WHERE of_dpt IN
(SELECT in_dpt FROM employee WHERE name='Tom');
```

##连接查询
* 在处理多个表时，子查询只有在结果来自一个表时才有用。但如果需要显示两个表或多个表中的数据，这时就必须使用连接 (join) 操作。连接的基本思想是把两个或多个表当作一个新的表来操作

```linux
mysql> select id,name,people_num from employee,department where employee.in_dpt=department.dpt_name order by id;
+----+------+------------+
| id | name | people_num |
+----+------+------------+
|  1 | Tom  |         15 |
|  2 | Jack |         12 |
|  3 | Rose |         10 |
|  4 | Jim  |         11 |
|  5 | Mary |         12 |
|  6 | Alex |         11 |
|  7 | Ken  |         11 |
|  8 | Rick |         10 |
|  9 | Joe  |         12 |
| 10 | Mike |         15 |
| 11 | Jobs |         12 |
| 12 | Tony |         10 |
+----+------+------------+
12 rows in set (0.00 sec)
另外一种SQL的书写方式是：
SELECT id,name,people_num
FROM employee JOIN department
ON employee.in_dpt = department.dpt_name
ORDER BY id;
```



