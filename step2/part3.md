# SQL优化
· 插入数据
1. insert优化
1) 批量插入数据(500-1k)
insert into tb_test value (1,'tom'),(2,'jack'),(3,'jane')
2) 手动提交事务
start transaction;
insert into tb_test value (1,'tom'),(2,'jack'),(3,'jane');
insert into tb_test value (4,'tom'),(5,'jack'),(6,'jane');
insert into tb_test value (7,'tom'),(8,'jack'),(9,'jane');
commit;
3) 主键顺序插入
主键乱序插入: 8 1 9 21 88 2 4 15 89 5 7 3
主键顺序插入: 1 2 3 4 5 7 8 9 15 21 88 89
顺序插入性能高于乱序插入
4) 大批量插入数据
如果一次性需要插入大批量数据使用Insert插入性能较低，可以使用MySQL的LOAD指令进行插入。

· 主键优化
1) 数据组织方式
在innodb中表数据都是根据主键顺序组织春芳的，这种存储方式称为索引组织表
![save](/step2/pic/save_jiegou.jpg)

2) 页(page)分裂
页可以为空也可以填充一半也可以100%。每个夜包含了2-N行数据,(如果一行数据勾搭会行溢出),根据初见排列

3) 页合并
当删除一行记录时，实际上记录没有被物理删除，只是记录被标记(flaged)为删除并且他的空间允许被其他记录声明使用。
当页纸删除的记录达到MERGE_THRESHOLD(默认为页的50%)，InnoDB会开始寻找最靠近的页(前或后)扣款是否可以将两个页合并成优化空间使用。
MERGE_THRESHOLD:合并页的法治，可以自己设置，在创建表或索引时候确定

· order by优化

· group by优化

· limit优化

· count优化

· update优化