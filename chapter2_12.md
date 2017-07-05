#2.12 视图，索引，备份
##0.索引
* 索引是一种与表有关的结构，它的作用相当于书的目录，可以根据目录中的页码快速找到所需的内容。
* 当表中有大量记录时，若要对表进行查询，没有索引的情况是全表搜索：将所有记录一一取出，和查询条件进行一一对比，然后返回满足条件的记录。这样做会消耗大量数据库系统时间，并造成大量磁盘 I/O 操作。
* 而如果在表中已建立索引，在索引中找到符合查询条件的索引值，通过索引值就可以快速找到表中的数据，可以大大加快查询速度。
* 对一张表中的某个列建立索引，有以下两种语句格式：
```linux
ALTER TABLE 表名字 ADD INDEX 索引名 (列名);
CREATE INDEX 索引名 ON 表名字 (列名);
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
##1.视图
* 