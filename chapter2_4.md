#2.4 Binlog
* binlog日志用于记录所有更新了数据或者已经潜在更新了数据（例如，没有匹配任何行的一个DELETE）的所有语句。语句以“事件”的形式保存，它描述数据更改。记录了所有的DDL（数据定义语言）和DML（数据操作语言）。
* 因为有了数据更新的binlog，所以可以用于实时备份，与master/slave复制
* 分类：
  * STATEMENT：日志只记录的都是语句，每一条对数据的修改的SQL都会记录在日志中。主从复制的时候，salve会将日志解析成原文本，salve重新执行这些SQL一遍。日质量少，对I/O的影响很少。缺点是在某些情况下salve的日志复制会出错。
  * ROW：它将每一行变更都记录在日志中，而不是记录SQL。例如：update table_a set name = 'abc'; 如果是STATEMENT日志只会记录一行SQL文本；但是如果是ROW，会对全表更新进行记录，那么如果这个表有1w行，那么ROW就会记录1W条的记录。日志量大大增加，优点就是记录每一行的变化，不会出现无法复制的情况，但是日志量很大，对I/O影响大。
  * MIXED：这种最新的默认的日志格式，混合来前面两种日志的优点，默认的情况下会时候STATEMENT格式的日志，但是在特殊的情况下是会使用ROW这种个格式的日志。


 %% Subgraph example
 graph TB
         subgraph one
         a1-->a2
         end
         subgraph two
         b1-->b2
         end
         subgraph three
         c1-->c2
         end
         c1-->a2