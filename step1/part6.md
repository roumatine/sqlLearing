# 事务 #
· 简介
    是一组操作的集合,是一个不可分割的工作单位,事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求,即这些操作要么同时成功,要么同时失败。
    案例: 银行转账
    流程: 1. 查询张三余额 2. 张三账户余额扣款 3. 李四账户余额增加
    异常: 1. 查询张三余额 2. 张三账户余额扣款 --->抛出异常 3. 李四账户余额增加(???)
    事务要控制流程1+2+3, 如果成功123则提交事务, 否则抛出异常回滚事务
    <!-- 默认MySQL是自动提交的, 也就是说当执行一条DML语句, MySQL会立即隐式自动提交事务。 -->

· 操作
1. 查看/设置事务提交方式
SELECT @@autocommit;
SET @@autocommit = 0;
2. 提交事务
   COMMIT;
3. 回滚事务
   ROLLBACK; 
4. 开启事务
   START TRANSACTION; 或 BEGIN;
<!-- 两种方式: 1. 关闭自动提交 2.开启事务 -->

· 四大特性 (ACID)
1. 原子性 (atomic) : 事务是不可分割的最小操作元,要么全部成功,要么全部失败。
2. 一致性 (consistency) : 事务完成时,必须使所有的数据都保持一致
3. 隔离性 (isolation) : 数据库提供的隔离机制,保证事务在不收外部并发操作影响的独立环境下运行
4. 持久性 (durability) : 事务一旦提交或回滚,对数据库的修改就是永久的。

· 并发问题 : 多个并发事务在执行过程中出现的问题
问题                    描述
脏读            
    一个事物读到另外一个事务还没有提交的数据
不可重复读          一个事务先后读取同一条记录,但两次读取的数据不同,称之为不可重读
幻读                一个按照条件查询数据时,没有对应数据行,但在插入数据时,又发现这行数据已经存在,好像出现'幻影'一样。

· 隔离级别
级别                脏读       不可重复读     幻读
READ UNCOMMITTED    TRUE      TRUE          TRUE
READ COMMITTED      FALSE     TRUE          TRUE (oracle默认)
REPEATABLE READ     FALSE     FALSE         TRUE (mysql默认)
SERIALIZABLE        FALSE     FALSE         FALSE
<!-- 隔离级别越高,数据越安全,性能越低 -->
1. 查看事务隔离级别
SELECT @@TRANSACTION_ISOLATION;
2. 设置事务隔离级别
SET [SESSION|GLOBAL] TRANSACTION ISOLATION LEVEL {READ COMMITTED};
