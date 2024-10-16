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

视图的更新
要使视图可更新，视图中的行与基础表中的行是一对一的关系。如果视图包含以下任何一项，则不可更新:
1. 聚合函数或窗口函数(sum,min,max,count等等)
2. DISTINCT
3. GROUP BY
4. HAVING
5. UNION或者UNION ALL

作用:
简单： 不仅可以简化用户对数据库的理解，也可以简化操作。那些警察被使用的查询可以被定义为视图，从而使用户不必为以后的操作每次指定全部条件
安全：数据库可以授权，但不能授权到数据库特定行和特定列上。通关视图用户只能查询和修改他们所能见到的数据
数据独立： 视图可以帮助用户屏蔽真是表结构变化带来的影响

## 存储过程
1. 介绍
存储过程是实现经过编译并存储在数据库中一段SQL语句的集合，调用存储过程可以简化开发人员的很多工作，减少数据在数据库和应用服务器之间的传输与，对于提高数据库存储的效率是有好处的。
简言之就是数据库SQL语言层面的代码封装和重用
2. 特点
1) 封装，服用
2) 可以接收参数，也可以返回数据
3) 减少网络交互，效率提升

3. 基本语法
1) 创建
CREATE PROCEDURE 存储过程名(参数列表)
BEGIN
    --SQL语句
END;
2) 调用
CALL 名称(参数)
3) 查看
SELECT * FROM INFOMATION_SCHEMA.ROUTINES WHERE ROUTINE_TYPE='xxx'; -- 查新知道数据库的存储过程及状态信息
SHOW CREATE PROCEDURE 存储过程名; -- 查询某个存储过程的定义
4) 删除
DROP PROCEDURE [IF EXISETS] 存储过程名;

<!-- 注：在命令行中，执行创建存储过程的SQL时，需要通过关键字delimiter来分隔存储过程体的结束 -->
4. 变量结构
1) 系统变量 是MYSQL服务器提供，不是用户定义的，属于服务器层面，分为全局变量(GLOBAL)、会话变量(SESSION), 默认为SESSION
查看系统变量
SHOW [GLOBAL|SESSION] VARIABLES; -- 查看所有系统变量
SHOW [GLOBAL|SESSION] VARIABLES LIKE '变量名'; -- 可以通过LIKE模糊匹配方式查找变量
SHOW @@[SESSION|GLOBAL] 系统变量名; -- 查看指定变量的值

设置系统变量
SET [GLOBAL|SESSION] 系统变量名=值;
SET @@[SESSION|GLOBAL] 系统变量名=值;

2) 用户定义变量： 是用户根据自己需要定义的变量，用户变量不用提前声明，在用的时候直接用"@变量名"使用即可，起作用域喂当前连接
赋值
SET @VAR_NAME = expr [,@var_name = expr]...;
SET @VAR_NAME := expr [,@var_name := expr]...;

SELECT @var_name := expr [,@var_name := expr]...;
SELECT 字段名 INTO @var_name FROM 表名;
使用
SELECT @var_name;

3) 局部变量： 是根据需要定义的在局部生效的变量，访问之前需要DECLEARE声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN...END块。
声明：
DECLARE 变量名 数据类型 [DEFAULT 值];
变量类型就是数据库字段类型：INT/BIGINT/CHAR/VARCHAR/DATE/TIME等等
赋值
SET 变量名 = 值;
SET 变量名 := 值;
SELECT 字段 INTO 变量 FROM 表;

5. if 条件判断
语法：
if 条件1 THEN
...
ELSEIF 条件2 THEN
...
ELSE
...
END IF;

6. 参数
类型        含义                                        备注
IN          该类参数作为输入，也就是需要调用时传入值        默认
OUT         该类参数作为输出，也就是该参数可以作为返回值
INOUT       该类参数既可以作为输入，也可以作为输出
用法：
CREATE PROCEDURE 存储过程鸣超([IN/OUT/INOUT 参数名 数据类型])
BEGIN
    -- SQL语句
END;

7. 流程控制 case
1) CASE case_value
    WHEN when_value1 THEN statement_list1
    [WHEN when_value2 THEN statement_list2] ...
    [ELSE statement_list]
END CASE;

2) CASE
    WHEN search_condition1 THEN statement_list1
    [WHEN search_condition2 THEN statement_list2] ...
    [ELSE statement_list]
END CASE;

8. 循环
1) while 
while 循环是有条件的循环控制语句。满足条件后在执行循环体中的SQL语句
-- 先判定条件，如果条件为true，则执行逻辑，否则不执行
WHILE 条件 DO
    SQL逻辑...
END WHILE;

2) repeat
repeat 是有条件的循环控制语句，当满足条件时退出循环：
-- 先执行一次逻辑然后判断逻辑是否满足，如果满足则推出，如果不满足则进行下一次循环
REPEAT 
    SQL逻辑... 
    UNTIOL 条件; 
END REPREAT;

3) loop
loop实现简单的循环，如果不在SQl逻辑中增加退出循环的条件，可以用来实现简单的死循。LOOP可以配以一下两个语句使用
LEAVE 配合循环使用，退出循环
ITERATE 必须用在循环中，作用是跳过当前循环剩下的语句至今进入下一次循环。

[begin_label:] LOOP
    SQL逻辑...
END LOOP [end_label];

LEAVE label; -- 推出指定标记的循环体
ITERATE label; --直接进入下一次循环


9. 游标(光标)
是用来存储查询结果集的数据类型，在存储过程和函数中可以使用游标对结果集尽孝循环的出路。游标使用包括游标的声明、OPEN、FETCH和CLOSE等
声明
DECLARE 游标鸣超 CURSOR FOR 查询语句;
打开
OPEN 游标名;
获取游标记录
FETCH 游标 INTO 变量;
关闭
CLOSE 游标名;

10. 条件处理程序
可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤
DECLARE handler_action HANDLER FOR [,condition_value]... statement;

handler_action
    CONTINUE: 继续执行当前程序
    EXIT : 终止执行当前程序
condition_value
    SQLSTATE sqlstate_value: 状态码，如02000
    SQLWARNING: 所有以01开头的SQLSTATE的代码简写
    NOT FOUND: 所有以02开头的SQLSTATE的代码简写
    SQLEXCEPTION: 所有没有被SQLWARING或NOT FOUND捕获的SQLSTATE代码的简写

## 存储函数
存储函数是有返回值的存储过程，存储函数的参数只能是IN类型
CREATE FUNTION 存储函数名称([参数列表])
RETUENS type [characteristic...]
BEGIN
    -- SQL语句
    RETURN ...
END

characteristic 说明：
DETERMINISTIC :相同的输入参数总是产生相同的结果
NO SQL : 不使用SQL语句，如：SELECT、INSERT、UPDATE、DELETE等
READS SQL DATA: 包含读取数据的语句但不包含写入数据的语句

不添加 characteristic 结果会有报错-- [HY000][1418] This function has none of DETERMINISTIC, NO SQL, or READS SQL DATA in its declaration and binary logging is enabled (you *might* want to use the less safe log_bin_trust_function_creators variable)

## 触发器
1. 定义
触发器是与表有关的数据库对象，指在insert/update/delete操作之前或之后，触发并执行触发器中定义的SQL语句集合。
触发器这种特性可以协助应用在数据库端确保数据的完整性，日志记录，数据校验等操作。
使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他数据库的内容是相似的。现在触发器还只是支持行级触发，不支持语句级触发

触发器类型      NEW和OLD
INSERT          NEW表示将要或者已经新增的数据
UPDATE          OLD表示修改之前的数据，NEW表示将要或者已经修改后的数据
DELETE          OLD表示将要或者已经删除的数据

2. 语法
1) 创建
CREATE TRIGGER tripgger_name
BEFORE|AFTER INSERT|UPDATE|DELETE ON table_name FOR EACH ROW -- 行级触发器
BEGIN
    trigger_statement;
END;
2) 查看
SHOW TRIGGERS;
3) 删除
DROP TRIGGER trigger_name; -- 如果没有指定SCHEMA_name则默认删除当前数据库