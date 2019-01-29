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
* 在排序的时候，desc只对它之前的列产生影响：
```linux
select name,age,phone from eployee order by name,age desc;
其中只会对age进行降序排列，不会对name产生影响。

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
## 其他一些操作
###1.查询的时候重复的结果只显示一次：
```linux
select distinct <字段> from <table_name>
```
###2.对于NULL的比较操作，应为NULL是特殊值，所以不能使用!=NULL来计较，要使用 is not NULL 来比较。
* 请注意在MySQL中，0或NULL意味着假而其它值意味着真。布尔运算的默认真值是1。
* 为了查询出哪个动物不再是活着的，使用death IS NOT NULL而不使用death != NULL的原因。
* 在GROUP BY中，两个NULL值被视为相同等价的。
* 执行ORDER BY语句排序时，如果运行ORDER BY ... ASC，则NULL值出现在最前面，若运行ORDER BY ... DESC，则NULL值出现在最后面。
* NULL操作的常见错误是认为不能在定义为NOT NULL的列内插入0或空字符串，但事实并非如此。这样的结果是在NULL表示"没有数值"的时候恰恰是有数值0存在的。因此使用IS [NOT] NULL则可以很容易地进行区分

###3.mysql中也可以使用正则表达式，使用的时候要使用的关键字是：REGEXP和NOT REGEXP操作符(或RLIKE和NOT RLIKE，它们是同义词)。
* 如果REGEXP模式与被测试值的任何地方匹配，模式就匹配(这不同于LIKE模式匹配，只有与整个值匹配，模式才匹配)。 为了定位一个模式以便它必须匹配被测试值的开始或结尾，在模式开始处使用“^”或在模式的结尾用“$”。
* 一些正则表达式的规则：
    * ‘.’匹配任何单个的字符。
    * 字符类“[...]”匹配在方括号内的任何字符。例如，“[abc]”匹配“a”、“b”或“c”。为了命名字符的范围，使用一个“-”。“[a-z]”匹配任何字母，而“[0-9]”匹配任何数字。
    * “ ”匹配零个或多个在它前面的字符。例如，“x”匹配任何数量的“x”字符，“[0-9]”匹配任何数量的数字，而“.”匹配任何数量的任何字符。
* 一些例子：
```linux
为了找出以“b”开头的名字，使用“^”匹配名字的开始：
select * from pet where name regexp '^b';
如果你想强制使REGEXP比较区分大小写，使用BINARY关键字使其中一个字符串变为二进制字符串。该查询只匹配名称首字母的小写‘b’。
select * from pet where name binary regexp '^b';
为了找出以“fy”结尾的名字，使用“$”匹配名字的结尾：
select * from pet where name regexp 'ly$';
为了找出包含一个“w”的名字，使用以下查询：
select * from pet where name regexp 'w';
为了找出包含正好5个字符的名字，使用“^”和“$”匹配名字的开始和结尾，和5个“.”实例在两者之间：
select * from pet where name regexp '^.....$';
你也可以使用“{n}”重复n次操作符,重写前面的查询：
select * from pet where name regexp '^.{5}$';
```


------------------------------------------------------------------
### 2019.1.29
#### 1.mysql 中 select 分为快照读和当前读
* select * from table_name where id = ? 这种是读取的数据库的快照版本，不加锁
* 下面是两种加锁的情况
    * select * from table_name where id = ? lock in share mode 这个会对读记录加共享锁（S锁）
    * select * from table_name where id = ? for update 这个会对读记录加怕他锁（X锁）

* serializable 隔离级别会在select 语句后面自动加 lock in share mode




