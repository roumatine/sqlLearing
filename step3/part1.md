# 日志
## 错误日志
介绍：
是MySQL最重要的日志之一，记录了当mysqld启动和停止时，以及服务器在运行过程中发生任何严重错误时的相关信息。
当数据库出现任何故障导致无法正常使用时，建议首先查看此日志
该日志默认开启，默认存放目录 `/var/log` 默认的日志文件名为 `mysqld.log`
查看日志位置： `show variables like '%log_error%';`

## 二进制日志


## 查询日志
## 慢查询日志
