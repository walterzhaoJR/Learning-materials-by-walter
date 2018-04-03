#MySQL索引背后的数据结构及算法原理
##1.摘要
* MySQL支持多种索引，本文只讨论MySQL中常用的BTree索引。哈希索引和全文索引等不讨论。
* 本文主要介绍：
    * 第一部分主要从数据结构及算法理论层面讨论MySQL数据库索引的数理基础。
    * 第二部分结合MySQL数据库中MyISAM和InnoDB数据存储引擎中索引的架构实现讨论聚集索引、非聚集索引及覆盖索引等话题。
    * 第三部分根据上面的理论基础，讨论MySQL中高性能使用索引的策略。
    
##2.数据结构及算法基础
###2.1索引的本质
* 索引就是为了高效获取数据的数据结构，看一个简单的示例。
 ![看一个简单的示例](http://blog.codinglabs.org/uploads/pictures/theory-of-mysql-index/1.png)
 
* 左边是数据表，一共有两列七条记录，最左边的是数据记录的物理地址（注意逻辑上相邻的记录在磁盘上也并不是一定物理相邻的）。为了加快Col2的查找，可以维护一个右边所示的二叉查找树，每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针，这样就可以运用二叉查找在O(log2n)的复杂度内获取到相应数据。但是在现实中的系统，没有人这样实现索引，包括不会使用红黑树来做为索引的数据结构。

* 一般会使用B-Tree或者B+Tree来实现索引。MySQL普遍使用B+Tree来实现索引。
* 一般在数据库系统或文件系统中使用的B+Tree结构都在经典B+Tree的基础上进行了优化，增加了顺序访问指针。如图：
![B+Tree示意图](http://blog.codinglabs.org/uploads/pictures/theory-of-mysql-index/4.png)
* 在B+Tree的每个叶子节点增加一个指向相邻叶子节点的指针，就形成了带有顺序访问指针的B+Tree。做这个优化的目的是为了提高区间访问的性能，例如图4中如果要查询key为从18到49的所有数据记录，当找到18后，只需顺着节点和指针顺序遍历就可以一次性访问到所有数据节点，极大提到了区间查询效率。

##3.MySQL索引实现
* 在MySQL中，索引属于存储引擎级别的概念，不同存储引擎对索引的实现方式是不同的，本文主要讨论MyISAM和InnoDB两个存储引擎的索引实现方式。

###3.1MyISAM索引实现
* MyISAM引擎使用B+Tree作为索引结构，叶节点的data域存放的是数据记录的地址。下图是MyISAM索引的原理图：
![MyISAM索引](http://blog.codinglabs.org/uploads/pictures/theory-of-mysql-index/8.png)
* 这里设表一共有三列，假设我们以Col1为主键，则上图是一个MyISAM表的主索引（Primary key）示意。可以看出MyISAM的索引文件仅仅保存数据记录的地址。在MyISAM中，主索引和辅助索引（Secondary key）在结构上没有任何区别，只是主索引要求key是唯一的，而辅助索引的key可以重复。如果我们在Col2上建立一个辅助索引，则此索引的结构如下图所示：
![辅助索引](http://blog.codinglabs.org/uploads/pictures/theory-of-mysql-index/9.png)

* 同样也是一颗B+Tree，data域保存数据记录的地址。因此，MyISAM中索引检索的算法为首先按照B+Tree搜索算法搜索索引，如果指定的Key存在，则取出其data域的值，然后以data域的值为地址，读取相应数据记录。

* MyISAM的索引方式也叫做“非聚集”的，之所以这么称呼是为了与InnoDB的聚集索引区分。

###3.2InnoDB的索引实现
* 虽然InnoDB也使用B+Tree作为索引结构，但具体实现方式却与MyISAM截然不同。

* 第一个重大区别是InnoDB的数据文件本身就是索引文件。从上文知道，MyISAM索引文件和数据文件是分离的，索引文件仅保存数据记录的地址。而在InnoDB中，表数据文件本身就是按B+Tree组织的一个索引结构，这棵树的叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键，因此InnoDB表数据文件本身就是主索引。

![InnoDB的索引](http://blog.codinglabs.org/uploads/pictures/theory-of-mysql-index/10.png)
* 可以看到叶节点包含了完整的数据记录。这种索引叫做聚集索引。因为InnoDB的数据文件本身要按主键聚集，所以InnoDB要求表必须有主键（MyISAM可以没有），如果没有显式指定，则MySQL系统会自动选择一个可以唯一标识数据记录的列作为主键，如果不存在这种列，则MySQL自动为InnoDB表生成一个隐含字段作为主键，这个字段长度为6个字节，类型为长整形。

* 第二个与MyISAM索引的不同是InnoDB的辅助索引data域存储相应记录主键的值而不是地址。换句话说，InnoDB的所有辅助索引都引用主键作为data域。例如，下图1为定义在Col3上的一个辅助索引：
![innode的辅助索引](http://blog.codinglabs.org/uploads/pictures/theory-of-mysql-index/11.png)
