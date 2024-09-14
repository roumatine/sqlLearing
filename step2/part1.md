# 存储引擎
1. MySQL体系结构
![体系结构图](/pic/part1.jpg)
· 连接层 : 最上层是一些客户端和连接服务,主要完成一些类似于连接处理、授权认证、及其相关的安全方案。服务器也会为安全接入的每个客户端严重其所具有的操作权限。
· 服务层 (核心功能) : 第二层架构是大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内函数的执行。所有夸存储引擎的功能也在这一层实现，如过程、函数等。
· 引擎层 (索引) : 存储引擎真正的负责了MySQL中数据的存储和提取,服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能,这样我们可以根据自己的需要,来选择合适的引擎
<!-- -- InnoDB是mysql5.0后默认引擎 : -->
· 存储层 (数据存储) : 主要将数据存储在磁盘上，并通过索引来快速定位数据。

2. 存储引擎简介
存储引擎是存储数据、建立索引、更新/查询数据等技术的视线方式。
存储引擎是基于表的，而不是基于库的，所以存储引擎可以被称为表类型。
   1) 在创建表时，指定存储引擎
    CREATE TABLE t1 (
        字段1 字段1类型 [COMMENT 字段1注释],
        ...
        字段n 字段n类型 [COMMENT 字段n注释]
    ) ENGINE=InnoDB;  [COMMENT 表注释]
   2) 查看当前数据库支持的存储引擎
    SHOW ENGINES;

3. 存储引擎特点
1) InnoDB 
介绍: 是一种兼顾高性能和高可靠性的通用存储引擎,在MySQL5.5后就是默认引擎
特点: DML操作遵循ACID模型,支持事务；
      行级锁,提高并发访问性能；
      支持外键 FOREIGN KEY约束,保证数据完整性和正确性
文件: xxx.ibd: xxx代表表名, innodb引擎的每张表都会对应这样一个表空间文件,存储该表的表结构(frm、sdi)、 数据和索引。
参数: innodb_file_per_table;

![innodb](/pic/innodb.jpg)
TableSpace: 表空间
Segment: 段
Extent: 区
Page: 页
Row: 行

2) MyISAM
介绍: 是MySQL早起的默认存储引擎
特点: 不支持事务,不支持外键;
      支持表锁,不支持行锁;
      访问速度快
文件: xxx.MYD 存储数据 / xxx.MYI 存储索引 / xxx.sdi 存储表结构信息
xxx.sdi 保存的json文本, 可以用`json.cn`格式化
3) Memory
介绍: 表数据存储在内存中,由于受硬件问题、或断电问题影响,只能将这些表作为临时表或缓存使用
特点: 内存存放    hash索引(默认)
文件： xxx.sdi : 存储表结构信息

![三个引擎的区别](/pic/diff.jpg)

4. 存储引擎选择
在纯粹引擎时，应根据英勇的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。
![选择](/pic/choose.jpg)