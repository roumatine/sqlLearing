# 锁
## 概述
锁是计算机语言协调多个进程或线程并发访问某一资源的机制
在数据库中，除传统的计算资源(CPU/RAM/I\O)的争用意外，数据也是一种供多用户共享的资源。
如果包子数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。
从这个角度来说锁对于数据库而言尤为重要，也更加复杂
分类：
1. 全局锁：锁定数据库所有表
2. 表级锁：每次操作所著整张表
3. 行级锁：每次操作所著对应的行数据

## 全局锁
介绍：
全局锁就是对整个数据库实例加锁，枷锁后整个实例就处于只读状态，后续的DML写语句,DLL语句，已经更新操作的事务提交语句都将被族所
其典型的使用场景就是做全库的逻辑备份，对所有的表进行锁定，从而获得一致性视图，保证数据完整性
`flush tables with read lock;`
`mysqldump [主机地址] -uroot -p1234 itcast > itcast.sql` -- -u指定用户名，-p指定密码，mysqldump是工具不是sql语句，需要在windows下执行
`unlock tables;`

特点：
数据库加全局锁是一个比较重的操作，会存在一下问题
1) 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上停摆
2) 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志(binlog)，会导致主从延迟
在innoDB中，我们可以在备份时候加上参数 --single-transaction，这样备份的表就只读，不会出现锁冲突，但是备份速度会变慢(本质上是快照读)
`mysqldump --single-transaction -uroot -p1234 itcast > itcast.sql`
## 表级锁
介绍：每次所住整张表。锁定力度大，发生锁冲突的概率最高，，并发度低。应用在MYISAM/InnoDB/BDB等存储引擎中
对于表锁，主要分为一下三类：
1) 表锁
表共享读锁(read lock)
表独占写锁(write lock)
语法: 
加锁    lock tables 表名... read/write;
释放锁  unlock tables / 客户端断开连接;
读锁不会阻塞其他客户端的读但是会阻塞写。写锁既会阻塞其他客户端的读，也会阻塞写

2) 元数据锁(meta data lock,MDL)
MDL加锁过程是系统自动控制，无需显示使用，在访问一张表的时候会自动加速。MDL锁主要作用是维护元数据的数据一致性，
在表上有活动事务的时候，不可以对元数据尽孝写入操作。为了避免DML和DDL冲突，保证读写正确性。
在MYSQl5.5中引入了MDL，当对一张表尽孝增删改查的时候加MDL读锁(共享)；当表结构尽孝变换操作的时候加MDL写锁(排他)
![MDL](/step2/pic/MDL.png)

查看元数据锁
`select object_type, object_schema, object_name, lock_type, lock_duration from performance_schema.metadata_locks;`

1) 意向锁
介绍：
为了避免MDL在执行时，加的行锁与表锁冲突，在InnoDB中引入了意向锁，是的表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查
意向贡献锁(IS)：由语句 select ... lock in share mode添加
与表锁贡献锁(read)兼容，与表锁排他锁(write)互斥

意向排他锁(IX)：由insert/update/delete/select ... for update添加
与表锁贡献锁(read)及排他锁(write)都互斥，意向锁直接不排除

查看意向锁机行锁的加锁情况：
`select object_schema, object_name, index_name, lock_type, lock_mode, lock_data from performance_schema.metadata_locks;`

## 行级锁
介绍：
每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在InnoDB中
InnoDB的数据是基于索引组织的，行级锁是通关对索引上的索引加锁来实现的，而不是对记录加的锁。
1. 行锁(Record Lock)
锁定单个行记录的锁，防止其他事务对此尽孝update和delete。在RC、RR(read commit ; reaptablt read)隔离级别下都支持

2. 间隙锁(Gap Lock)
锁定索引记录间隙(不含该记录)，别抱索引记录间隙不变，防止其他食物在这个间隙进行insert，产生幻读。在RR隔离级别下都支持

3. 临键锁(Next Key Lock))
行锁和间隙锁组合，同时锁住数据并锁住数据前面的间隙Gap。在RR隔离级别下支持

InnoDB实现了一下两种类型行锁
1. 共享锁(S)：允许一个事务去读一行，阻止其他食物获得相同数据集的排他锁
2. 排他锁(X)：允许获取排他锁的食物更新数据，组织其他食物获得相同数据集的共享锁和排他锁

锁类型\请求锁类型   S(共享锁)   X(排他锁)
S(共享锁)           兼容        冲突
X(排他锁)           冲突        冲突

![hang_lk](/step2/pic/hang_lock.jpg)

行锁：
默认情况下InnoDB在 reaptablt read 的事务隔离级运行，InnoDB使用next-key锁进行搜索和索引扫描，以防幻读。
1. 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动有华为行级锁
2. InnoDB的行级锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁

查看意向锁及行锁的加锁情况：
`select object_schema, object_name, index_name, lock_type, lock_mode, lock_data from performance_schema.metadata_locks;`

间隙锁/临键锁
默认情况下InnoDB在 reaptablt read 的事务隔离级运行，InnoDB使用next-key锁进行搜索和索引扫描，以防幻读。
1. 索引上进行等值查询时(唯一索引)，给不存在的记录加锁时，优化为间隙锁
2. 索引上进行等值查询时(普通索引)，向右遍历时最后一个值不满足查询需求时,next_key lock优化为间隙锁
3. 索引上进行等范围询时(唯一索引)-- 会访问到不满足条件的第一个值位置

<!-- 注：间隙锁唯一目的是防止其他食物插入间隙。间隙锁可以共存，一个食物采用的间隙锁不会阻止另一个事务在同一间隙上的间隙锁。 -->