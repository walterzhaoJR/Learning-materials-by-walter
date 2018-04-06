#数据库中的页
##1.Innodb中页的概念
###1.1基础结构
* Page是Innodb存储的最基本结构，也是Innodb磁盘管理的最小单位，与数据库相关的所有内容都存储在Page结构里。Page分为几种类型：数据页（B-Tree Node），Undo页（Undo Log Page），系统页（System Page），事务数据页（Transaction System Page）等；每个数据页的大小为16kb，每个Page使用一个32位（一位表示的就是0或1）的int值来表示，正好对应Innodb最大64TB的存储容量(16kb * 2^32=64tib)
* 一个Page的基本结构如下：
![Innode的页](https://segmentfault.com/img/bVJ1hx?w=468&h=446)

###1.2头部数据
* 每个page都有通用的头和尾，但是中部的内容根据page的类型不同而发生变化，头部的数据如下：
![头部数据](https://segmentfault.com/img/bVJ1hz?w=1336&h=696)
* page头部保存了两个指针，分别指向前一个Page和后一个Page，头部还有Page的类型信息和用来唯一标识Page的编号。根据这个指针分布可以想象到Page链接起来就是一个双向链表
![双向链表](https://segmentfault.com/img/bVJ1hF?w=884&h=284)

###1.3主体数据
* 在Page的主体部分，主要关注数据和索引的存储，他们都位于User Records部分，User Records占据Page的大部分空间，User Records由一条条的Record组成，每条记录代表索引树上的一个节点（非叶子节点和叶子节点）；在一个单链表的内部，单链表的头尾由两条记录来表示，字符串形式的“ Infimum”代表开头，“Supremum”表示结尾；System Record 和 User Record是两个平行的段；
Innodb中存在四种不同的Record，分别是

    * 主键索引树非叶子节点

    * 主键索引树叶子节点

    * 辅助键索引树非叶子节点

    * 辅助键索引树叶子节点

* 这四种节点Record格式上有差异，但是内部都存储着Next指针指向下一个Record