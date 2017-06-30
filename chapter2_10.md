#2.10 约束
##约束是一种限制，它通过对表的行或列的数据做出限制，来确保表的数据的完整性、唯一性。
##约束是一种限制，它通过对表的行或列的数据做出限制，来确保表的数据的完整性、唯一性。MySql中通常有如下几种约束：


|约束类型   |关键字|
|---------|------|
|主键|PRIMARY KEY|
|默认值|DEFAULT|
|唯一值|UNIQUE|
|外键|FOREIGN KEY|
|非空|NOT NULL|

***
##具体介绍相关约束
###主键
* 约束是一种限制，它通过对表的行或列的数据做出限制，来确保表的数据的完整性、唯一性。
```linux
mysql> create table ta ( id int(10) primary key, name varchar(10) );
或者
create table tb( id int(10) not null, name varchar(10) not null, primary key(name));
```

###默认值
* 默认值约束 (DEFAULT) 规定，当有 DEFAULT 约束的列，插入数据为空时，将使用默认值。
```linux
create table tc ( id int(10) primary key, name varchar(10) default 'testname',phone varchar(10));
insert into tc(id,name,phone) values(1,'walter','12345');
insert into tc(id,phone) values(2,'12354');
mysql> select*from tc;
+----+----------+-------+
| id | name     | phone |
+----+----------+-------+
|  1 | walter   | 12345 |
|  2 | testname | 12354 |
+----+----------+-------+
2 rows in set (0.00 sec)
```
* 如果设置了默认值约束，在insert数据时，如果插入值，那么将覆盖默认值，如果没有插入，则使用默认值。

###唯一约束
* 
唯一约束 (UNIQUE) 比较简单，它规定一张表中指定的一列的值必须不能有重复值，即这一列每个值都是唯一的
```linux
create table td( id int, phone varchar(11), primary key(id), unique(phone) );
insert into td(id,phone) values(1,'12345');
insert into td(id,phone) values(2,'12346');
insert into td(id,phone) values(3,'12346');
ERROR 1062 (23000): Duplicate entry '12346' for key 'phone'
```

###外键约束
* 外键 (FOREIGN KEY) 既能确保数据完整性，也能表现表之间的关系。
* 一个表可以有多个外键，每个外键必须 REFERENCES (参考) 另一个表的主键，被外键约束的列，取值必须在它参考的列中有对应值。
* 在 INSERT 时，如果被外键约束的值没有在参考列中有对应则INSERT 失败

### 非空约束
* 非空约束 (NOT NULL),听名字就能理解，被非空约束的列，在插入值时必须非空。
```linux
create table te( id int not null, phone varchar(10), primary key(id) );
insert into te(id,phone) values(1,'12345');
insert into te(id,phone) values('123456');
ERROR 1136 (21S01): Column count doesn't match value count at row 1
mysql> insert into te(id,phone) values(null,'123456');
ERROR 1048 (23000): Column 'id' cannot be null
```

