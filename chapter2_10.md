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

