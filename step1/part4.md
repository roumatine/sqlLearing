# 约束 #
1. 概念: 约束是作用于表字段上的规则, 用于限制存执在表中的数据
2. 目的: 保证数据库中数据的正确、有效和完整性
3. 分类:
   约束                        描述                         关键字
   非空约束              限制该字段数据不能为null           NOT NULL
   唯一约束              保证该字段的所有数据的唯一性        UNIQUE
   主键约束              主键是一行数据的唯一标识符          PRIMARY KEY
   默认约束   保存数据时, 如果未指定改字段的值, 采用默认值    DEDEFAULT
   检查约束              保证字段值满足某一个条件            CHECK(条件1 and 条件2)
   外键约束 用来让两张表的数据之间建立连接, 保证数据的一致性和完整性 FOREIGN KEY
<!-- 注:约束是作用于表中字段上的, 可以在创建表/修改表的时候添加约束 -->
<!-- auto_increment:自增 -->

## 外键约束 ##
员工表:

子表
id name job salary entydate managerid deptid 


deptid ---> id

父表
id name


· 添加外键
CREAT TABLE 表名(
    字段名 数据类型,
    ...
    [constraint] [外键约束名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表字段名)
);
ALTER TABLE 表名 ADD CONSTRAINT 外键约束名称 FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)；

· 删除外键
ALTER TABLE 表名 DROP CONSTRAINT 外键约束名称;


删除/更新行为

行为                        说明
NO ACTION                  当在附表中删除/更新对应记录时,首先检查该记录是否有对应外键,如果有则不允许删除/更新.(与RESTRICT相同)
RESTRICT                   当在附表中删除/更新对应记录时,首先检查该记录是否有对应外键,如果有则不允许删除/更新.(与NO ACTION相同)
CASCADE                    当在附表中删除/更新对应记录时,首先检查该记录是否有对应外键,如果有,则同时删除/更新外键在子表中对应记录.
SET NULL                   当在附表中删除对应记录时,首先检查该记录是否有对应外键,如果有,则将该外键在子表中对应记录置为NULL.(要求该外键允许为null)
SET DEFAULT                父表有变更时,子表将外键列设置成一个默认值(Innodb不支持)

ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (外键字段) REFERENCES 父表名(父表字段名) ON DELETE RESTRICT ON UPDATE RESTRICT;