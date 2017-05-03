#2.7 Mysql分区操作
自Mysql5.1开始对分区(Partition)有支持。
## 2.7.1 水平分区（根据列属性按行分）
举个简单例子：一个包含十年发票记录的表可以被分区为十个不同的分区，每个分区包含的是其中一年的记录。
### 水平分区的几种模式：
* Range（范围） – 这种模式允许DBA将数据划分不同范围。例如DBA可以将一个表通过年份划分成三个分区，80年代（1980's）的数据，90年代（1990's）的数据以及任何在2000年（包括2000年）后的数据。 

* Hash（哈希） – 这中模式允许DBA通过对表的一个或多个列的Hash Key进行计算，最后通过这个Hash码不同数值对应的数据区域进行分区，。例如DBA可以建立一个对表主键进行分区的表。 

* Key（键值） – 上面Hash模式的一种延伸，这里的Hash Key是MySQL系统产生的。 

* List（预定义列表） – 这种模式允许系统通过DBA定义的列表的值所对应的行数据进行分割。例如：DBA建立了一个横跨三个分区的表，分别根据2004年2005年和2006年值所对应的数据。 

* Composite（复合模式） - 是以上模式的组合使用。举例：在初始化已经进行了Range范围分区的表上，我们可以对其中一个分区再进行hash哈希分区。 

## 2.7.2 垂直分区（按列分）
举个简单例子：一个包含了大text和BLOB列的表，这些text和BLOB列又不经常被访问，这时候就要把这些不经常使用的text和BLOB了划分到另一个分区，在保证它们数据相关性的同时还能提高访问速度。
## 2.7.3 分区示例
* 创建分区表,按日期的年份拆分
```sql
mysql> CREATE TABLE part_tab ( c1 int default NULL, c2 varchar(30) default NULL, c3 date default NULL) engine=myisam 
PARTITION BY RANGE (year(c3)) (PARTITION p0 VALUES LESS THAN (1995),
PARTITION p1 VALUES LESS THAN (1996) , PARTITION p2 VALUES LESS THAN (1997) ,
PARTITION p3 VALUES LESS THAN (1998) , PARTITION p4 VALUES LESS THAN (1999) ,
PARTITION p5 VALUES LESS THAN (2000) , PARTITION p6 VALUES LESS THAN (2001) ,
PARTITION p7 VALUES LESS THAN (2002) , PARTITION p8 VALUES LESS THAN (2003) ,
PARTITION p9 VALUES LESS THAN (2004) , PARTITION p10 VALUES LESS THAN (2010),
PARTITION p11 VALUES LESS THAN MAXVALUE ); 
```
注意最后一行，考虑到可能的最大值
* 创建未分区表
```sql
mysql> create table no_part_tab (c1 int(11) default NULL,c2 varchar(30) default NULL,c3 date default NULL) engine=myisam;
```

