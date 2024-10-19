# 日志
## 错误日志
介绍：
是MySQL最重要的日志之一，记录了当mysqld启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。
当数据库出现任何故障导致无法正常使用时，建议首先查看此日志
该日志默认开启，默认存放目录 `/var/log` 默认的日志文件名为 `mysqld.log`
查看日志位置： `show variables like '%log_error%';`

## 二进制日志
介绍：
简称(BINLOG)记录了所有的DDL(数据定义语言)语句和DML(数据操作语言)语句，但是不包括数据库查询(select/show)

作用：
1. 暂难时的数据恢复
2. MySQL的主从复制
默认二进制日志开启，查询指令
`show variables like '%log_bin%';`

日志格式：                      含义
STATEMENT               基于SQL语句的日志记录，记录的是SQL语句对数据进行修改的SQL都会记录在日志文件中
ROW                     基于行的日志记录，记录的是每一行的数据变更(默认)
MIXED                   混合日志记录，默认采用了STATEMENT格式,在某些特殊情况下会自动切换为ROW进行记录

查询默认日志格式：
`show variables like '%binlog_format%'`

日志查看：
由于日志是以二进制方式存储的，不能直接读取，需要通过二进制日志查询工具mysqlbinlog来查看
![binlog](/step2/pic/mysqlbinlog.jpg)

msyqlbinlog [参数] logfilename
参数选择：
-d  指定数据库名称，只列出指定的数据库相关操作
-o  忽略掉日志中的前n行命令
-v  将行事件(数据变更)重构为SQL语句
-w  将行事件(数据变更)重构为SQL语句，并输出注释信息

日志删除
对于较为繁忙的业务系统每天生成的binlog数据巨大，如果长时间不清楚将会占用大量磁盘空军

指令                                                含义
reset master;                                       删除全部binlog日志，删除之后，日志编号将从binlog.000001重新开始
purge master logs to 'binlog.******';               删除******编号之前的所有日志
purge master logs before 'yyyy-mm-dd hh24:mi:ss';   删除日志为 yyyy-mm-dd hh24:mi:ss 之前的所有日志

## 查询日志
介绍：
查询日志中记录了客户端所有操作语句，而二进制日志不包含查询数据的SQL语句。
默认情况下查询日志是未开启的
查询日志：
show variables like '%general%'
修改MYSQL配置文件'etc/my.cnf'文件添加以下内容：
-- 该选项用来开启查询日志
general_log = 1
-- 设置日志的文件名，如果没有指定，默认为host_name.log
general_log_file = mysql_query.log

## 慢查询日志
介绍：
慢查询日志记录所有执行时间超过参数 long_query_time 的设置值兵乓球扫描记录不小于 min_examined_row_limit的所有SQL语句
默认未开启。long_query_time默认时间为10秒，最小为1秒，精度可以到微秒
-- 慢查询日志
long_query_log = 1
-- 执行时间参数
long_query_time = 2

默契情况下，不会记录管理语句，也不会记录不适应索引进行查找的查询。
可以使用log_slow_admin_statements和更改此行为 log_queries_not_using_indexes
-- 记录执行较慢的管理语句
log_slow_admin_statements = 1
-- 记录执行较慢的未使用索引的语句
log_queries_not_using_indexes = 1