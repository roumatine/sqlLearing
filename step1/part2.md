# SQL

## 通用语法 ##

1. 单行/多行书写，分号结尾
2. 可以用空格/缩进来增加可读性
3. mysql中sql不区分大小写，关键字建议大写
4. 注释: 单行注释 -- 或 # 
    多行注释 /* */

## 分类 ##

1. DDL 定义语句，定义数据库对象
2. DML 操作语言，对表中数据进行操作
3. DCL 控制语句，创建数据库用户、库访问权限
4. DQL 查询语句，查询表记录

### DDL ###

1. 查询
    SHOW DATABASES; // 查询所有数据库
    SELECT DATABASE(); // 查询当前数据库

2. 创建
   CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEAFULT CHARACTERSET 字符集] [COLLATE 排序规则];

3. 删除
   DROP DATABASE [IF EXISTS] 数据库名;
4. 使用
   USE 数据库名;

# 表操作 #

1. 查询
    SHOW TABLES; // 查询当前数据库所有表
    DESC 表名; // 查询表结构
    SHOW COLUMNS FROM 表名; // 查询知道表的建表语句

2. 创建
   CREAT TABLE 表名(
        字段1 字段1类型[COMMENT 字段1注释],
        字段2 字段2类型[COMMENT 字段2注释],
        ……
        字段n 字段n类型[COMMENT 字段n注释]
   )[COMMENT 表注释];
<!-- mysql> create table user(
    -> id int comment '编号',
    -> name varchar(50) comment '姓名',
    -> age int comment '年龄',
    -> gender varchar(1) comment '性别'
    -> ) comment '用户表'; -->
3. 修改
   · 添加字段
    ALTER TABLE 表名 ADD 字段名 字段类型(长度) [COMMENT 注释] [约束];
<!-- alter table employee nickname varchar(20) comment '昵称'; -->

   · 修改数据类型
    ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);

   · 修改字段名和字段类型
   ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];
<!-- alter table employee change nickname username varchar(30) comment '用户名'; -->

    · 删除字段
    ALTER TABLE 表名 DROP COLUMN 字段名;
<!-- alter table employee drop username; -->

    · 修改表名
    ALTER TABLE 表名 RENAME TO 新表名;
<!-- alter table employee rename to emp; -->

    · 删除表
    DROP TABLE [IF EXISTS] 表名;
    · 删除指定表，并重新创建该表
    TRUNCATE TABLE 表名;