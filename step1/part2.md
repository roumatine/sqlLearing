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

### DML ###

· 增加数据(insert)
1. 给指定字段添加数据
   INSERT INTO 表名 (字段1, 字段2) VALUES (值1,值2,……);
2. 给全部字段添加数据
   INSERT INTO 表名 VALUES (值1,值2,…);
3. 批量添加数据
   INSERT INTO 表名 (字段1，字段2) VALUES (值1,值2,…),(值1,值2,…),(值1,值2,…);
   INSERT INTO 表名 VALUES (值1,值2,…),(值1,值2,…); 当前表中所有字段
   <!-- 字符串和日期型应该包含在引号内,插入数据大小应该在字段的规定范围内 -->

· 修改数据(update)
<!-- 修改条件可以没有,如果没有则修改整张表的所有数据 -->
   UPDATE 表名 SET 字段1=值1,字段2=值2,… [WHERE 条件]; 
· 删除数据(delete)
<!-- 条件可以没有,如果没有则删除整张表的所有数据 -->
<!-- DELETe语句不能删除某一字段的值(可以用Update) -->
   DELETE FROM 表名 [WHERE 条件];

### DQL ###
查询数据库表中记录
#### 编写顺序
SELECT
    字段列表
FROM
    表名列表
WHERE
    条件列表
GROUP BY
    分组字段列表
HAVING
    分组后条件列表
OEDER BY
    排序字段列表
LIMIT
    分页参数

· 基本查询
1. 查询多个字段
   SELECT 字段1,字段2 FROM 表名;
   SELECT * FROM 表名; 查询所有字段
2. 设置别名
   SELECT 字段1 [AS 别名1],字段2 [AS 别名2] FROM 表名;
3. 去除重复记录(去重操作)
   SELECT DISTINCT 字段列表 FROM 表名;
   
· 条件查询(WHERE)
1. SELECT 字段列表 FROM 表名 WHERE 条件列表;
2. 比较运算符               功能
    >                       大于
    >=                      大于等于
    <                       小于
    <=                      小于等于
    =                       等于
    <>或!=                  不等于
    BETWEEN ... AND ...     在某个范围之内(含最小、最大值)
    IN(...)                 在in之后的列表中的的值,最多选一
    LIKE                    模糊查询, %通配符, _匹配单个字符
    IS NULL                 判断是否为空

    逻辑运算符
    AND或&&                 并且(多个条件同时成立)
    OR或||                  或者(多个条件任意一个成立)
    NOT或!                  非,不是

· 聚合函数(count、max、min、avg、sum)
1. 介绍: 将一列数据作为一个整体, 纵向计算
2. 常见函数
   函数     功能
   count    统计数量
   max      最大值
   min      最小值
   avg      平均值
   sum      求和
3. 语法
   SELECT 聚合函数(字段列表) FROM 表名;
   <!-- 所有null值不计入计算 -->

· 分组查询(GROUP BY)
1. 语法
   SELECT 字段列表 FROM 表名[WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件];
2. where和having区别
   1. 执行时机不同: where在分组前执行, having在分组后执行
   2. 判断条件不同: where不能对聚合函数进行判断, having可以
   
· 排序查询(ORDER BY)
1. 语法
   SELECT 字段列表 FROM 表名 ORDER BY 排序字段名 排序方式1, 字段2 排序方式2;
2. 排序方式
   ASC：升序(默认值)
   DESC：降序
   <!-- 如果是多字段排序，当第一个字段相同时，才会根据第二字段进行排序 -->

· 分页查询(LIMIT)
1. 语法
   SELECT 字段列表 FROM 表名 LIMIT 起始索引,查询返回的记录数;
   <!-- 其实索引从0开始, 起始索引 = (查询页码 - 1) * 每页显示记录数;
   分页查询是数据库方言, 不同数据库员不同实现, MySQL实现方式是 limit;
   如果查询的是第一页数据,其实索引可以忽略, 直接简写为limit 10。 -->

#### 执行顺序
SELECT
   字段列表         *4
FROM
   表名列表         *1
WHERE
   条件列表         *2
GROUP BY
   分组字段列表     *3
HAVING
   分组后条件列表
OEDER BY
   排序字段列表     *5
LIMIT
   分页参数         *6
===========================
FROM
   表名列表
WHERE
   条件列表
GROUP BY
   分组字段列表
HAVING
   分组后条件列表
SELECT
   字段列表
OEDER BY
   排序字段列表
LIMIT
   分页参数

### DCL ###
控制访问权限
· 用户管理
1. 查询用户
   USE mysql;
   SELECT * FROM user;
2. 创建用户
   CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
3. 修改用户密码
   ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';
4. 删除用户
   DROP USER '用户名'@'主机名';