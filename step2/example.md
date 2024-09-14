# 存储引擎
## 存储引擎简介
-- 查询建表语句 --- 默认存储引擎: InnoDB
show create table account;

-- 查询当前数据库支持的存储引擎
SHOW ENGINES;

-- 创建表 my_myisam, 并指定MyISAM存储引擎
create table my_myisam(
    id int,
    name varchar(10)
) engine = MyISAM;

-- 创建表 my_memory , 指定Memory存储引擎
create table my_memory(
    id int,
    name varchar(10)
) engine = Memory;

## 
--
show variables like 'innodb_file_per_table';
-- 查询本地xxx.ibd
`ibd2sdi account.ibd`