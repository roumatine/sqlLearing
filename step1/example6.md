# 事务
## 操作
create table account(
    id int auto_increment primary key comment '主键id',
    name varchar(10) comment '姓名',
    money int comment '余额'
) comment '账户表';
insert into account(id, name, money) values
    (null, '张三', 2000), (null, '李四', 2000);

-- 恢复数据
update account set money = 2000 where name = '张三' or name = '李四';
select @@autocommit; -- 结果为1表明是自动提交
set @@autocommit = 0; -- 设置为手动提交

-- 转账操作
-- 1. 查询张三余额
select * from account where name = '张三';
-- 2. 将张三余额 -1000
update account set money = money -1000 where name = '张三';
-- 3. 李四账户 +1000
update account set money =  money + 1000 where name = '李四';

-- 模拟异常 -- >
select * from account where name = '张三';
update account set money = money -1000 where name = '张三';
程序抛出异常...
update account set money =  money + 1000 where name = '李四';
-- 张三 -1000 但是李四没有 +1000, 此时每一条都是自己的事务,会自动提交反馈在表内

-- 提交事务
commit ;
-- 回滚事务
rollback ;

## -- 事务隔离级别
### 脏读
1. A事务:
set session transcation isolation level read uncommitted;
select * from account;
start transcation;
select * from account;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | 张三 |  2000 |
|  2 | 李四 |  2000 |
+----+------+-------+

2. B事务:
start transaction;
update account set money = money - 1000 where name = '张三';

3. A事务:
select * from account;
+----+------+-------+
| id | name | money |
+----+------+-------+
|  1 | 张三 |  1000 |
|  2 | 李四 |  2000 |
+----+------+-------+
未进行commit就对进行数据修改;
commit后 : |  1 | 张三 |  1000 | 
