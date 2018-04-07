#2.17 数据库中schema的概念
##1.1一些不同的解释
###1.1.1
* 在学习数据库时，会遇到一个让人迷糊的Schema的概念。实际上，schema就是数据库对象的集合，这个集合包含了各种对象如：表、视图、存储过程、索引等。
* 如果把database看作是一个仓库，仓库很多房间（schema），一个schema代表一个房间，table可以看作是每个房间中的储物柜，user是每个schema的主人，有操作数据库中每个房间的权利，就是说每个数据库映射user有每个schema（房间）的钥匙。

###1.1.2
* 对于mysql，schema和database可以理解为等价的.
* As defined in the MySQL Glossary:In MySQL, physically, a schema is synonymous with a database. You can substitute the keyword SCHEMA instead of DATABASE in MySQL SQL syntax, for example using CREATE SCHEMA instead of CREATE DATABASE.
* Some other database products draw a distinction. For example, in the Oracle Database product, a schema represents only a part of a database: the tables and other objects owned by a single user.

###1.1.3
* 官方定义如下：
* A schema is a collection of database objects (used by a user.).
* schema objects are the logical structures that directly refer to the database’s data.
* A user is a name defined in the database that can connect to and access objects.
* schemas and users help database administrators manage database security.