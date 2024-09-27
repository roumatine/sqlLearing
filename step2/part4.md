# 视图/存储过程/触发器
## 视图
视图(view)是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表。并且是在使用视图是动态生成的。
视图只保存了查询SQL逻辑，不保存查询结果。所以在创建视图时，主要工作在创建这条SQL查询语句上
1. 创建视图
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION]
2. 查询视图
查看创建视图语句 : SHOW CREATE VIEW 视图名称;
查看视图数据 : SELECT * FROM 视图名称;
3. 修改视图
方式一: CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION]
方式二: ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [WITH [CASCADED | LOCAL] CHECK OPTION]
4. 删除视图
DROP VIEW [IF EXISTS] 视图名称 ...
5. 检查选项
当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如插入，更新，删除，以使其符合视图的定义。MySQL允许基于另一个视图创建视图，它还会检查以来视图中的规则以保持一致性。为了确定检查范围，MySQL提供了两个选择: CASCADED和LOCAL。默认值为CASCADED。


## 存储过程
## 存储函数
## 触发器