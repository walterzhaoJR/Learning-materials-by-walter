#2.12 视图，索引，备份,数据导入
##0.索引
* 索引是一种与表有关的结构，它的作用相当于书的目录，可以根据目录中的页码快速找到所需的内容。
* 当表中有大量记录时，若要对表进行查询，没有索引的情况是全表搜索：将所有记录一一取出，和查询条件进行一一对比，然后返回满足条件的记录。这样做会消耗大量数据库系统时间，并造成大量磁盘 I/O 操作。
* 而如果在表中已建立索引，在索引中找到符合查询条件的索引值，通过索引值就可以快速找到表中的数据，可以大大加快查询速度。
* 对一张表中的某个列建立索引，有以下两种语句格式：
```linux
ALTER TABLE 表名字 ADD INDEX 索引名 (列名);
CREATE INDEX 索引名 ON 表名字 (列名);
例如：
mysql> alter table employee add index idx_id (id);
mysql> create index idx_name on employee (name);
可以使用 show index from <table_name>来查看索引
mysql> show index from employee;
+----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table    | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| employee |          0 | PRIMARY  |            1 | id          | A         |           5 |     NULL | NULL   |      | BTREE      |         |               |
| employee |          0 | phone    |            1 | phone       | A         |           5 |     NULL | NULL   |      | BTREE      |         |               |
| employee |          1 | emp_fk   |            1 | in_dpt      | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
| employee |          1 | idx_id   |            1 | id          | A         |           5 |     NULL | NULL   |      | BTREE      |         |               |
| employee |          1 | idx_name |            1 | name        | A         |           5 |     NULL | NULL   | YES  | BTREE      |         |               |
+----------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
5 rows in set (0.00 sec)
```
* 在使用SELECT语句查询的时候，语句中WHERE里面的条件，会自动判断有没有可用的索引。

##1.视图
* 视图是从一个或多个表中导出来的表，是一种虚拟存在的表。它就像一个窗口，通过这个窗口可以看到系统专门提供的数据，这样，用户可以不用看到整个数据库中的数据，而只关心对自己有用的数据。
* 注意理解视图是虚拟的表：
    * 数据库中只存放了视图的定义，而没有存放视图中的数据，这些数据存放在原来的表中；
    * 使用视图查询数据时，数据库系统会从原来的表中取出对应的数据；
    * 视图中的数据依赖于原来表中的数据，一旦表中数据发生改变，显示在视图中的数据也会发生改变；
    * 在使用视图的时候，可以把它当作一张表。
* 创建视图的语句格式为：
```linux
CREATE VIEW 视图名(列a,列b,列c) AS SELECT 列1,列2,列3 FROM 表名字;
```
* 可见创建视图的语句，后半句是一个SELECT查询语句，所以视图也可以建立在多张表上，只需在SELECT语句中使用子查询或连接查询，这些在之前的实验已经进行过。
```linux
mysql> create view v_emp (v_name,v_age,v_phone) as select name,age,phone from employee;
mysql> select*from v_emp;
+--------+-------+---------+
| v_name | v_age | v_phone |
+--------+-------+---------+
| Tom    |    26 |  119119 |
| Jack   |    24 |  120120 |
| Jobs   |  NULL |   19283 |
| Tony   |  NULL |  102938 |
| Rose   |    22 |  114114 |
+--------+-------+---------+
5 rows in set (0.00 sec)
```

##2.导入
* 导入操作，可以把一个文件里的数据保存进一张表。
```linux
LOAD DATA INFILE '文件路径' INTO TABLE 表名字;
需要导入的数据按照什么字符结尾：
load data infile '文件路径' into table 表名字 terminated '结尾的字符'
```
* 导入的时候一般会出现如下问题：
```linux
The MySQL server is running with the --secure-file-priv option so it cannot execute this statement
```
这是需要设定导出导入目录，在my.cnf中的--secure-file-priv中设置。
例如：
```linux
secure_file_priv="/Users/walter/Desktop"
```

##3.导出
* 导出与导入是相反的过程，是把数据库某个表中的数据保存到一个文件之中。
```linux
SELECT 列1，列2 INTO OUTFILE '文件路径和文件名' FROM 表名字;
```

##4.备份
* 数据库中的数据或许十分重要，出于安全性考虑，在数据库的使用中，应该注意使用备份功能。
* 备份与导出的区别：导出的文件只是保存数据库中的数据；而备份，则是把数据库的结构，包括数据、约束、索引、视图等全部另存为一个文件。
mysqldump 是 MySQL 用于备份数据库的实用程序。它主要产生一个 SQL 脚本文件，其中包含从头重新创建数据库所必需的命令CREATE TABLE INSERT 等。
* 备份的语句
```linux
mysqldump -u root 数据库名>备份文件名;   #备份整个数据库
mysqldump -u root 数据库名 表名字>备份文件名;  #备份整个表
```

##5.恢复
* 用备份文件恢复数据库
```linux
source path+file_name.sql
```

