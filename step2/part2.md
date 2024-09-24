# 索引
· 索引概述(高效获取数据的数据结构)
1. 介绍:(index)是帮助MySQL搞笑获取数据的数据结构(有序)。在数据之外,数据库系统还维护满足特定查找算法的数据结构,这些数据结构以某种方式引用(指向)数据。
2. 优缺点
            优势                                劣势
提高数据检索效率,降低数据库IO成本           索引列也是要占空间的
通过索引列对数据进行排序,降低数据排序的成    大大提高查找效率,同时也降低更新表的速度,
本,降低CPU的消耗。                         如对表进行INSERT\UPDATE\DELETE时,效率降低

· 索引结构
MySQL的索引是在存储引擎实现,不同的存储引擎有不同结构,主要包含一下几种:
索引结构        描述
* B+TREE索引      最常见的索引,大部分引擎索引都支持B+树索引
* HASH索引        底层数据结构是用哈希表实现的,只有精确到匹配索引列的数据才有效,不支持范围查询
R-TREE索引      空间索引是MyISAM引擎的一个特殊索引类型,主要用于地理空间数据类型,通常使用较少
FULL-TEXT索引   是一种通过建立倒排索引,快速匹配文档的方式。类似于Lucene/Solr/ES


索引类型        InnoDB                   MyISAM                MEMORY
B+TREE索引      支持                     支持                  支持
HASH索引        不支持                   不支持                支持
R-TREE索引      不支持                   支持                  不支持
FULL-TEXT索引   5.6以上支持              支持                  不支持
<!-- 平常所说索引如没有特别指明,默认为B+树索引 -->

## B+TREE索引
二叉树缺点:顺序插入时,会形成单链表,查询性能大大降低。大数据量情况下,层级较深,检索速度慢
红黑树: 大数据量情况下,层级较深,检索速度慢

B-TREE(多路平衡查找树)
以一颗最大度数(max-degree,指一个节点的子节点个数)为5(5阶)的b-tree树为例(每个节点最多存储4个key->五个指针)
![b-tree](/step2/pic/b-tree.jpg)

`https://www.cs.usfca.edu/~galles/visualization/Algorithms.html # 数据结构可视化网站`

B+TREE
以一颗最大度数(max-degree)为4阶的b+tree树为例
![b+tree](/step2/pic/b+tree.jpg)

和B-Tree的区别:
1) 所有数据都会出现在叶子节点
2) 叶子节点形成单向链表

MySQL索引对经典的B+Tree进行优化。在原B+Tree的基础上,增加一个指向相邻子节点的链表指针,就形成了带有顺序指针的B+Tree,提高区间访问的性能。
![mysql_B+Tree](/step2/pic/mysql_B+Tree.jpg)

## HASH索引
哈希索引是采用一定的hash算法,将键值换算成新的hash值,映射到对应的槽位上,然后存储在hash表中。
如果两个(或多个)键值,映射到一个相同槽位时,产生hash冲突，可以通过链表来解决
![hash](/step2/pic/hash.jpg)
特点:
1) Hash索引只能用于对等比较(=, in), 不支持查询范围(between, >, <, ...)
2) 无法利用索引完成排序操作
3) 查询效率高,通草只需要一次检索就可以,效率通常高于B+Tree索引
在MySQL中，支持hash索引的是Memory引擎，而InnoDB中具有自适应hash功能，hash索引是存储引擎根据B+Tree索引在指定条件下自动构建的
<!-- 思考: -->
<!-- 为什么InnoDB存储引擎选择使用B+TREE索引结构 -->
<!-- 相对于二叉树，层级更少，搜索效率更高 -->
<!-- 对于B-tree无论是叶子节点还是非叶子节点，都会保存数据，这导致一页中存储的键值减少，指针跟着减少，要童谣保持大量书籍，只能增加树的高度，导致性能降低 -->
<!-- 相对于Hash索引，B+tree支持范围匹配及排序操作 -->

· 索引分类
分类            含义                            特点                    关键字
主键索引    针对表中主键创建的索引              默认自动创建，只能有一个    PRIMARY
唯一索引    避免同一个表中某数据列中的值重复     可以有多个                 UNIQUE
常规索引    快速定位特定数据                    可以有多个
全文索引    全文索引查抄的是文本中的关键词，     可以有多个                 FULLTEXT
                而不是比较索引中的值

在InnoDB中，根据索引的存储形式又可以分为以下两种:
1) 聚簇索引(Clustered Index)
将数据存储与索引放到一块,索引结构的叶子节点保存了行数据
特点:必须有而且只有一个
<!-- 选取规则:
如果存在主键，则默认为聚簇索引；
如果不存在主键，则将使用为唯一索引(Uni1que)作为聚集索引；
如果表没有主键或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为因此的聚集索引。 -->

2) 二级索引(Secondary Index)
将数据与索引分开存储，索引结构的叶子节点快练得是对应的主键
特点:可以存在多个
![索引分类](/step2/pic/Clustered.jpg)

select * from user where name = 'Arm';
    回表查询: 先从二级索引找到对应主键值，根据主键值到聚集索引找到对应行的行数据
<!-- 思考 -->
<!-- 以id为主键，name字段创建的有索引 -->
<!-- 1.select* from user where id = 10; 和 2.select * from user where name = 'Arm' -->
<!-- 哪一个执行效率高，为什么  -->

<!-- InnoDB主键索引的B+Tree 高度多高? -->
<!--  -->

· 索引语法
1) 创建索引
CREATE [UNIQUE|FULLTEXT] INDEX index_name ON table_name (index_column_name,...);
2) 查看索引
SHOW INDEX FROM table_name;
3) 删除索引
DROP INDEX index_name ON table_name;

· SQL性能分析(工具)
1) SQL执行评率
通过 show [session|global] status 命令提供服务器状态信息，可以查看当前数据库的INSERT, UPDATE, DELETE, SELECT的访问频次：
`SHOW GLOBAL STATUS LIKE 'Com_______';`  
<!-- 一个下划线代表一个字符 -->

2) 慢查询日志
记录了所有执行时间超过指定参数(long_query_time,单位:秒,默认10秒)的所有SQL语句的日志。
`show variables like 'slow_query_log';` 查询开关是否开启
MySQL的慢查询日志默认 没有开启 ,需要在Mysql的配置文件(/etc/my.cnf)中配置如下信息:
`slow_query_log = 1 # 开启MySQL慢日志查询开关`
`lone_query_time = 2 # 设置慢日志查询时间为2秒,SQL 执行时间超过2秒的SQL语句将被记录到慢查询日志中`
后重启MySQL服务进行测试,日志记录信息在/var/lib/mysql/localhost-slow.log中
<!-- 根据mysql版本不同搜索关键词来查询配置文件,并对该配置进行修改 -->
<!-- grep -rl "slow_query_log" /etc/mysql/ -->

3) profile详情
show profile 可以在SQL优化时了解时间耗费。通过have_profiling参数看到当前MySQL是否支持profile功能。
`SELECT @@have_profiling; #是否支持`
`SELECT @@profiling; 是否打开`
默认profiling是关闭的,需要通过set语句在session/global中开启:
`SET profiling = 1;`
查看每一条SQL耗时的基本情况：
`show profiles;`
查看指定query_id的SQL语句各个阶段的耗时情况:
`show profile for query query_id;`
查看指定query_id的SQL语句CPU使用情况:
`show profile cpu for query query_id;`

4) explain 执行计划
EXPLAIN或者DESC命令获取MySQL如何执行SELECT语句的信息,包括SELECT语句执行过程中表入伙连接和连接的顺序。
直接在select语句之前加上关键字 explain/desc
`EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件;`

![explain](/step2/pic/explain.jpg)
EXPLAIN执行计划的各字段含义：
id: select查询的序列号,表示查询中执行select子句或者是操作表的顺序(id相同，执行顺序从上到下，id不同，值越大，越先执行)

select_type: 执行计划中的SQL语句的类型，有：SIMPLE、PRIMARY、SUBQUERY、DEPENDENT SUBQUERY、UNION、UNION RESULT、DERIVED、MATERIALIZED、TEMPORARY

type: 表示连接类型，性能由好到差类型为 :
NULL, system, const, eq_ref, ref,
<!-- fulltext, ref_or_null, index_merge, unique_subquery, index_subquery, -->
range, index, ALL
(业务系统中基本不会优化到NULL->不访问任何表)
(system系统表)
(主键、唯一索引基本出现const)
(非唯一性质索引ref)
(全表扫描ALL,index)

possible_key
显示可能应用在这张表上的索引，一个或多个

key
实际使用的索引，如果为NULL，则没有使用索引

key_len
表示索引中使用的字节数，该值为索引字段最大可能长度，并非实际使用长度，
在不损失精确性的前提下，长度越短越好

rows
MySQL认为必须要实质性查询的行数，在innodb引擎的列表中，是一个估计值

filtered
表示返回结果的行数占需读取行数的百分比,filtered的值越大越好

· 索引使用
1) 验证索引效率
在未建立索引之前执行如下SQL语句，查看SQL耗时
SELECT * FROM tb_sku WHERE sn = '100000003145001';

2) * 最左前缀法则
如果索引了多列(联合索引)要遵循最左前缀法则，值查询从索引的最左侧开始，不跳过索引中的列
如果跳跃某一列，索引将部分失效(后面字段索引失效)

![left](/step2/pic/left_role.jpg)

3) 范围查询 
联合索引中，出现范围查询(>,<)，范围查询右侧的列索引失效

![space](/step2/pic/space.jpg)

4) 索引列运算
不要在索引列上进行运算操作，否则索引将失效
<!-- select * from tb_user where substring(phone, 9,2) = '21'; -->

5) 字符串不加引号
字符串类型字段使用时，不加引号将会失效
<!-- explain select * from tb_user where phone = '1779990019';
explain select * from tb_user where phone = 1779990019; -->

6) 模糊查询
仅仅只是尾部模糊查询索引不会失效，如果是头部模糊匹配，索引失效
explain select * from tb_user where profession like '软件%';
explain select * from tb_user where profession like '%工程';

7) or连接条件
用or分割开的条件，如or前的条件中的列有索引，而后面的列中没有索引，那么涉及到的索引都不会背用到
<!-- explain select * from tb_user where id = 10 or age = 23;
explain select * from tb_user where phone = '1779990014' or age = 23; -->
由于age没有索引，所以即使id、phone有索引，索引也会失效，所以需要针对age也要建立索引
<!-- create index idx_user_age on tb_user(age); -->

8) 数据分布影响
如果MySQL评估适用索引比全表更慢，则不适用索引
<!-- explain select * from tb_user where phone >= '17799990000'; -->

9) SQL提示
是优化数据库的一个重要手段，就是在SQL语句中添加索引提示，告诉MySQL如何使用索引(默认优先是联合索引)
<!-- create index idx_user_pro on tb_user(profession); -->
use index：
`explain select * from tb_user use index(idx_user_pro) where proffesion = '软件工程';`

ignore index：
`explain select * from tb_user ignore index(idx_user_pro) where proffesion = '软件工程';`

force index：
`explain select * from tb_user force index(idx_user_pro) where proffesion = '软件工程';`

10) 覆盖索引
尽量使用覆盖索引(查询使用了索引并且需要返回列，在该索引中已经全部能找到)，减少使用select *
![cover](/step2/pic/cover.jpg)

`explain select id,(profession,age,status,)name from tb_user where profession = '软件工程' and age = 32 and status = '0';`
会在Extra 会出现using index condition;
using index condition: 查找使用了索引，但是需要<回表查询>数据
using whrer; using index: 查找使用了索引，但是需要的数据都支索引列中能找到，所以不需要回表查询数据

![huibiao](/step2/pic/huibiao.jpg)

11) 前缀索引 
当字段类型为字符串(varchar, text等)时，有时候需要索引很长的字符串，这会让索引变得很大，查询时浪费大量磁盘IO，影响查询效率。此时可以只将字符串一部分前缀建立索引，这样可以打打节约索引空间，提高效率
语法:
create index idx_xxxx on table_name(column_name(prefix_len))
前缀长度:
可以根据索引的选择性来决定，而选择性是指不重复的索引值和数据表的记录总数的壁纸，索引选择性越高则查询效率越高。唯一索引的选择性是1，这是最好的索引选择性，也是性能最好的。
select count(distinct email) / count(*) from tb_user;
select count(distinct substring(email, 1, 5)) / count(*) from tb_user;
前缀索引查询流程:
![qianzhui](/step2/pic/qianzhui.jpg)

12) 单列索引与联合索引
单列索引:一个索引包含单个列
联合索引:一个索引包含多个列
在业务场景，如果存在多个查询条件，考虑针对查询字段建立索引时，建议建立联合索引，而非单列索引

· 索引设计原则
1. 针对数据量较大(十百万以上)，且查询比较频繁的表建立索引
2. 针对常作为查询条件(where)、排序(order by)、分组(group by)操作的字段建立索引
3. 尽量选择区分度高的列表作为索引，尽量建议唯一索引，区分度越高，使用索引的效率越高
4. 如果是字符串的类型字段，且字段长度较长，可以针对字段特点建立前缀索引
5. 尽量使用联合索引，减少单列索引，查询时联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高效率
6. 要控制索引的数量，索引不是多多益善，索引越多维护索引结构的代价越大，会影响增删改的效率
7. 如果索引列不能存储NULL值，请在创建表时使用NOT NULL来约束他，当优化器指定每列是否包含NULL值时他可以更好的确定那个索引更有效率的用于查询