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


8.查看数据表中的数据
```linux
select * from <tableName>
select * from employee
```



