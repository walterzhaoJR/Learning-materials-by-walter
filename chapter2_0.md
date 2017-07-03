#2.0 数据库基础操作

0.开启数据库服务
```linux
service mysql start
```
1.创建数据库
```linux
语句格式为 CREATE DATABASE <数据库名字>;
create database mysql_walter;
```

2.显示数据库
```linux
show databases;
```

3.切换数据库（选择数据库）
```linux
语句格式为 CREATE DATABASE <数据库名字>;
use mysql_walter;
```
4.显示数据表
```
show tables;
```
5.创建数据表
```linux
CREATE TABLE 表的名字
(
列名a 数据类型(数据长度),
列名b 数据类型(数据长度),
列名c 数据类型(数据长度)
);
例如：
CREATE TABLE employee 
(
id int(10),
name char(20),
phone int(12)
);
```

6.mysql中数据类型char和varchar的区别：
* CHAR 的长度是固定的
* VARCHAR 的长度是可以变化的
* 比如，存储字符串 “abc"，对于 CHAR(10)，表示存储的字符将占 10 个字节(包括 7 个空字符)，而同样的 VARCHAR(12) 则只占用4个字节的长度，增加一个额外字节来存储字符串本身的长度，12 只是最大值，当你存储的字符小于 12 时，按实际长度存储。

7.mysql中数据类型enum和set的区别
* ENUM 类型的数据的值，必须是定义时枚举的值的其中之一，即单选。
* 而 SET 类型的值则可以多选。

8.查询数据表中的数据
```linux
select * from <tableName>;
select * from employee;
```

9.向数据表中插入数据
```linux
INSERT INTO 表的名字(列名a,列名b,列名c) VALUES(值1,值2,值3);
例如：
insert into employee(id,name,phone) values(02,'yn',1761234);
```

10.注意，在数据插入的时候：
* 数据类型是 CHAR,VARCHAR,TEXT,DATE,TIME,ENUM 等类型的数据也需要单引号修饰，而 INT,FLOAT,DOUBLE 等则不需要。

11.在插入的时候有些数据可以为空（前提是，这些字段允许为空）：
```linux
INSERT INTO employee(id,name) VALUES(03,'Rose');
```
上面的这个例子中，查询可得：第三行，phone字段为NULL。
```linux
+------+------+-----------+
| id   | name | phone     |
+------+------+-----------+
|    1 | Tom  | 110110110 |
|    2 | yn   |   1761234 |
|    3 | jj   |      NULL |
+------+------+-----------+
3 rows in set (0.00 sec)
```

12.删除数据库
```linux
drop database <数据库名字>
drop database mydb;
```

13.可以将SQL语句写入*.sql文件中，然后在mysql的控制台中使用source 局对路径/文件名 来执行sql语句。

14.select语句可以使用where来添加查找条件，比如or、and、in、not in等：
```linux
smysql> select name,age,phone,in_dpt from employee where in_dpt in('dpt3','dpt4');
+------+------+--------+--------+
| name | age  | phone  | in_dpt |
+------+------+--------+--------+
| Tom  |   26 | 119119 | dpt4   |
| Rose |   22 | 114114 | dpt3   |
| Rick |   24 | 987654 | dpt3   |
| Mike |   23 | 110110 | dpt4   |
| Tony | NULL | 102938 | dpt3   |
+------+------+--------+--------+
5 rows in set (0.00 sec)

mysql> select name,age,phone,in_dpt from employee where in_dpt not in('dpt3','dpt4');
+------+------+--------+--------+
| name | age  | phone  | in_dpt |
+------+------+--------+--------+
| Jack |   24 | 120120 | dpt2   |
| Jim  |   35 | 100861 | dpt1   |
| Mary |   21 | 100101 | dpt2   |
| Alex |   26 | 123456 | dpt1   |
| Ken  |   27 | 654321 | dpt1   |
| Joe  |   31 | 110129 | dpt2   |
| Jobs | NULL |  19283 | dpt2   |
+------+------+--------+--------+
7 rows in set (0.00 sec)
```

##数据库及表的修改和删
1.重命名一张表
```linux
RENAME TABLE 原名 TO 新名字;
ALTER TABLE 原名 RENAME 新名;
ALTER TABLE 原名 RENAME TO 新名;
```
2.删除一张表
```linux
drop table <table_name>
```
3.对一列的修改(即对表结构的修改)
* 3.1增加一列
```linux
ALTER TABLE 表名字 ADD COLUMN 列名字 数据类型 约束;
或： ALTER TABLE 表名字 ADD 列名字 数据类型 约束;
mysql> alter table table_1 add column l_4 int(10) not null;
或者:
mysql> alter table table_1 add l_5 int(10) default 0;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> desc table_1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| l_1   | int(10) | NO   | PRI | NULL    |       |
| l_2   | int(10) | YES  |     | NULL    |       |
| l_3   | int(10) | YES  |     | NULL    |       |
| l_4   | int(10) | NO   |     | NULL    |       |
| l_5   | int(10) | YES  |     | 0       |       |
+-------+---------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```

* 可以发现：新增加的列，被默认放置在这张表的最右边。如果要把增加的列插入在指定位置，则需要在语句的最后使用AFTER关键词(“AFTER 列1” 表示新增的列被放置在 “列1” 的后面)。
```linux
mysql> alter table table_1 add l_6 int(20) not null after l_1;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> desc table_1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| l_1   | int(10) | NO   | PRI | NULL    |       |
| l_6   | int(20) | NO   |     | NULL    |       |
| l_2   | int(10) | YES  |     | NULL    |       |
| l_3   | int(10) | YES  |     | NULL    |       |
| l_4   | int(10) | NO   |     | NULL    |       |
| l_5   | int(10) | YES  |     | 0       |       |
+-------+---------+------+-----+---------+-------+
6 rows in set (0.00 sec)
```

* 如果想放在第一列的位置，则使用 FIRST 关键词，如语句：
```linux
mysql> alter table table_1 add l_0 int(10) not null first;
Query OK, 0 rows affected (0.05 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> desc table_1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| l_0   | int(10) | NO   |     | NULL    |       |
| l_1   | int(10) | NO   | PRI | NULL    |       |
| l_6   | int(20) | NO   |     | NULL    |       |
| l_2   | int(10) | YES  |     | NULL    |       |
| l_3   | int(10) | YES  |     | NULL    |       |
| l_4   | int(10) | NO   |     | NULL    |       |
| l_5   | int(10) | YES  |     | 0       |       |
+-------+---------+------+-----+---------+-------+
7 rows in set (0.00 sec)
```

* 3.2删除表中的一列和刚才使用的新增一列的语句格式十分相似，只是把关键词 ADD 改为 DROP ，语句后面不需要有数据类型、约束或位置信息。具体语句格式：
```linux
mysql> alter table table_1 drop l_0;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> desc table_1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| l_1   | int(10) | NO   | PRI | NULL    |       |
| l_6   | int(20) | NO   |     | NULL    |       |
| l_2   | int(10) | YES  |     | NULL    |       |
| l_3   | int(10) | YES  |     | NULL    |       |
| l_4   | int(10) | NO   |     | NULL    |       |
| l_5   | int(10) | YES  |     | 0       |       |
+-------+---------+------+-----+---------+-------+
6 rows in set (0.00 sec)
```

* 3.3重命名一列：这条语句其实不只可用于重命名一列，准确地说，它是对一个列做修改(CHANGE) 
* 注意：这条重命名语句后面的 “数据类型” 不能省略，否则重命名失败。
当原列名和新列名相同的时候，指定新的数据类型或约束，就可以用于修改数据类型或约束。需要注意的是，修改数据类型可能会导致数据丢失，所以要慎重使用。
```linux
ALTER TABLE 表名字 CHANGE 原列名 新列名 数据类型 约束;
mysql> alter table table_1 change l_7 l_5 int(10) not null default 10;
```

* 3.4改变数据类型，要修改一列的数据类型，除了使用刚才的CHANGE语句外，还可以用这样的MODIFY语句
* 再次提醒，修改数据类型必须小心，因为这可能会导致数据丢失。在尝试修改数据类型之前，请慎重考虑。
```linux
ALTER TABLE 表名字 MODIFY 列名字 新数据类型;
mysql> alter table table_1 modify l_5 int(2);
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> desc table_1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| l_1   | int(10) | NO   | PRI | NULL    |       |
| l_6   | int(20) | NO   |     | NULL    |       |
| l_2   | int(10) | YES  |     | NULL    |       |
| l_3   | int(10) | YES  |     | NULL    |       |
| l_4   | int(10) | NO   |     | NULL    |       |
| l_5   | int(2)  | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
6 rows in set (0.00 sec)
```

4.对表的内容修改





