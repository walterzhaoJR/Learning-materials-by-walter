#Sql审计
##背景：
假设这么一个情况，你是某公司mysql-DBA，某日突然公司数据库中的所有被人为删了。
尽管有数据备份，但是因服务停止而造成的损失上千万，现在公司需要查出那个做删除操作的人。
但是拥有数据库操作权限的人很多，如何排查，证据又在哪？
是不是觉得无能为力？
mysql本身并没有操作审计的功能，那是不是意味着遇到这种情况只能自认倒霉呢？
本文就将讨论一种简单易行的，用于mysql访问审计的思路。

##概述
其实mysql本身已经 提供了详细的sql执行记录–general log，但是开启它有以下几个缺点：
* 无论sql有无语法错误，只要执行了就会记录，导致记录大量无用信息，后期的筛选有难度。
* sql并发量很大时，log的记录会对io造成一定的印象，是数据库效率降低。
* 日志文件很容易快速膨胀，不妥善处理会对磁盘空间造成一定影响。

##解决方式
* 使用init-connect + binlog的方法进行mysql的操作审计。
* 由于mysql binlog记录了所有对数据库长生实际修改的sql语句，及其执行时间，和connection_id但是却没有记录connection_id对应的详细用户信息。
*init-connect，在每次连接的初始化阶段，记录下这个连接的用户，和connection_id信息。在后期审计进行行为追踪时，根据binlog记录的行为及对应的connection-id 结合 之前连接日志记录 进行分析，得出最后的结论。

##具体实现
### 1. 设置init-connect
* 1.1 创建用于存放连接日志的数据库和表
 ```sql
create database accesslog;
CREATE TABLE accesslog.accesslog (`id` int(11) primary key auto_increment, `time` timestamp, `localname` varchar(30), `matchname` varchar(30))
```

* 1.2 创建用户权限
可用现成的root用户用于信息的读取
```sql
grant read on accesslog.* to root@localhost identified by ‘password’;
```
如果存在具有to \*.\* 权限的用户需要进行限制。
当前登录用户需要对accesslog库至少具有insert权限
* 1.3 设置init-connect
在[mysqld]下添加以下设置：
```sql
init-connect='insert into accesslog.accesslog 
values(connection_id(),now(),user(),current_user());'
```
注意insert句子的语法、引号正确，如果错误的话，登录到mysql之后，操作db会提示你与服务器连接丢失。
Eg.提示信息
```linux
  No connection. Trying to reconnect...
Connection id:    220
Current database: *** NONE ***
 
 ERROR 2013 (HY000): Lost connection to MySQL server during query
```
log-bin--------如果原来的配置文件已经启用了日志，这里省略
1.4 重启数据库生效
```linux
shell> service mysqld restart
```
###2.记录追踪
* 2.1 thread_id确认
假设想知道在2009年11月25日，上午9点多的时候，是谁吧test.dummy这个表给删了。可以用以下语句定位
```sql
mysqlbinlog –start-datetime='2009-11-25 09:00:00' –stop-datetime='2009-11-25 09:00:00'  binlog.xxxx | grep 'dummy' -B 5
```
会得到如下结果(可见thread_id为5)：
```sql
# at 300777
#091124 16:54:00 server id 10  end_log_pos 301396       Query   thread_id=5     exec_time=0     error_code=0
SET TIMESTAMP=1259052840;
drop table test.dummy;
```
* 2.2 用户确认
thread_id 确认以后，找到元凶就只是一条sql语句的问题了。
```sql
select * from accesslog.accesslog where conn_id=5 ;
```
就能发现是testuser2@localhost干的了。
```sql
+——+——————————-+——————————-+—————————–+
| id   | time                | localname           | matchname          |
+——+——————————-+——————————-+—————————–+
|   5  | 2009-11-25 10:57:39 | testuser2@localhost | testuser2@%        |
+——+——————————-+——————————-+—————————–+
``` 
###3.Q&A
* Q：使用init-connect会影响服务器性能吗？
 * A：理论上，只会在用户每次连接时往数据库里插入一条记录，不会对数据库产生很大影响。除非连接频率非 常高（当然，这个时候需要注意的就是如何进行连接复用和控制，而非是不是要用这种方法的问题）
* Q：access-log表如何维护?
 * A: 由于是一个log系统，推荐使用archive存储引擎，有利于数据厄压缩存放。如果数据库连接 数量很大的话，建议一定时间做一次数据导出，然后清表。
* Q：表有其他用途么？
 * A：有！access-log表当然不只用于审计，当然也可以用于对于数据库连接的情况进行数据分析，例如每日连接数分布图等等，只有想不到没有做不到。
* Q：会有遗漏的记录吗？
 * A：会的，init-connect 是不会在super用户登录时执行的。所以access-log里不会有数据库超级用户的记录，这也是为什么我们不主张多个超级用户，并且多人使用的原因。