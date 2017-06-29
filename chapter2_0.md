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






