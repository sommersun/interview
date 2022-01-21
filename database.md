## 💾 数据库


### 关系型数据库与非关系型数据库

 高并发读写能力差：网站类用户的并发性访问非常高，而一台数据库的最大连接数有限，且硬盘 I/O 有限，不能满足很多人同时连接。  
 NOSQL  
 键值数据库：Redis、Memcached、Riak  
 文档数据库：MongoDB、CouchDB、MarkLogic  


### 常用 SQL 操作

<table>
  <tr>
    <th>对象类型</th>
    <th>对象</th>
    <th>操作类型</th>
  </tr>
  <tr>
    <td rowspan="4">数据库模式</td>
    <td>模式</td>
    <td><code>CREATE SCHEMA</code></td>
  </tr>
  <tr>
    <td>基本表</td>
    <td><code>CREATE SCHEMA</code>，<code>ALTER TABLE</code></td>
  </tr>
    <tr>
    <td>视图</td>
    <td><code>CREATE VIEW</code></td>
  </tr>
    <tr>
    <td>索引</td>
    <td><code>CREATE INDEX</code></td>
  </tr>
    <tr>
    <td rowspan="2">数据</td>
    <td>基本表和视图</td>
    <td><code>SELECT</code>，<code>INSERT</code>，<code>UPDATE</code>，<code>DELETE</code>，<code>REFERENCES</code>，<code>ALL PRIVILEGES</code></td>
  </tr>
    <tr>
    <td>属性列</td>
    <td><code>SELECT</code>，<code>INSERT</code>，<code>UPDATE</code>，<code>REFERENCES</code>，<code>ALL PRIVILEGES</code></td>
  </tr>
</table>

> SQL 语法教程：[runoob . SQL 教程](http://www.runoob.com/sql/sql-tutorial.html)



#### 索引

* 数据库索引：顺序索引、B+ 树索引、hash 索引
* [MySQL 索引背后的数据结构及算法原理](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)
为什么要使用B+tree作为索引? (一般)
索引文件的形式存储的磁盘上,查询时间主要取决于磁盘I/O的存取次数，而不是存取一次的时间，预读的长度一般为页（page）的整倍数，所以B+tree的大小为1页。  
MySQL索引实现  
MyISAM：叶节点的data域存放的是数据记录的地址，非聚集索引  
InnoDB: 叶节点data域保存了完整的数据记录。这个索引的key是数据表的主键,聚集索引，因此自增且短的字段适合做主键  
最左前缀原理：在检索数据时从联合索引的最左边开始匹配  


#### 范式

* 第一范式（1NF）：属性（字段）是最小单位不可再分。
* 第二范式（2NF）：满足 1NF，每个非主属性完全依赖于主键（消除 1NF 非主属性对码的部分函数依赖）。
* 第三范式（3NF）：满足 2NF，任何非主属性不依赖于其他非主属性（消除 2NF 非主属性对码的传递函数依赖）。


### 并发控制

* 事务是并发控制的基本单位。  
* 悲观锁与乐观锁  
  悲观锁是使用数据库提供的锁，在读取数据之前上锁。  
  乐观锁是在最后修改数据再进行冲突检查，方法有版本控制，时间戳
* 可串行化调度：多个事务的并发执行是正确的，当且仅当其结果与按某一次序串行地执行这些事务时的结果相同。  

### 锁
* 锁类型：排他锁（X 锁 / 写锁）、共享锁（S 锁 / 读锁）。
* 活锁死锁：
    * 活锁：互相谦让资源
    * 死锁：互相占用对方的资源  
解决方法： 同一顺序加锁、超时放弃

### 事务的ACID 
原子性 一致性 隔离性 持久性
一致性：破坏关系数据的完整性以及业务逻辑上的一致性

### 数据不一致性

脏读： 脏读是指一个事务在处理数据的过程中，读取到另一个未提交事务的数据

不可重复读： 不可重复读是指对于数据库中的某个数据，一个事务范围内的多次查询却返回了不同的结果，这是由于在查询过程中，数据被另外一个事务修改并提交了。

幻读：是事务非独立执行时发生的一种现象。例如事务T1对一个表中所有的行的某个数据项做了从“1”修改为“2”的操作，这时事务T2又对这个表中插入了一行数据项，而这个数据项的数值还是为“1”并且提交给数据库。而操作事务T1的用户如果再查看刚刚修改的数据，会发现还有一行没有修改，其实这行是从事务T2中添加的，就好像产生幻觉一样，这就是发生了幻读。
幻读和不可重复读都是读取了另一条已经提交的事务（这点就脏读不同），所不同的是不可重复读查询的都是同一个数据项，而幻读针对的是一批数据整体（比如数据的个数）。  

### 隔离等级  

未提交读（Read Uncommited）  
“排他写锁”，但是不排斥读线程实现  

已提交读（Read Commited）  
如果是一个读事务(线程)，则允许其他事务读写，如果是写事务将会禁止其他事务访问该行数  

可重复读（Repeatable Read)  
读取数据的事务将会禁止写事务(但允许读事务)，写事务则禁止任何其他事务(包括了读写)，因为是行级锁所以会出现幻读  

行级锁  
锁表  

Mysql 默认 Repeatable read（可重复读）  

SET Transaction Isolation Level xxxx  
<a id="design-pattern"></a>
