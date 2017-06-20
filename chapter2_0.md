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


