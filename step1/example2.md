# 根据需求创建表(设计合理的数据类型)

· 设计一张员工信息表，要求如下:
    1. 编号(纯数字)
    2. 员工工号(字符串类型，长度不超过10位)
    3. 员工姓名(字符串类型，长度不超过10位)
    4. 性别(男/女，储存一个汉字)
    5. 年龄(正常人年龄，不可能存储负数)
    6. 身份证号(二代身份证号均为18位,带有x这样的字符)
    7. 入职时间(取值年月日即可)

create table employee(
    id int comment '编号',
    job_number varchar(10) comment'工号',
    name varchar(10) comment '姓名',
    sex char(1) comment '性别',
    age tinyint unsigned comment '年龄',
    idcard char(18) comment '身份证号',
    entrydate date comment '入职时间'
) comment '员工表';



## 数据准备
create table employee(
    id              int             comment '编号',
    worknum         varchar(10)     comment'工号',
    name            varchar(10)     comment '姓名',
    sex             char            comment '性别',
    age             tinyint unsigned comment '年龄',
    idcard          char(10)        comment '身份证号',
    workaddress     varchar(50)     comment '工作地址',
    entrydate       date            comment '入职时间'
)comment '员工表';

insert into employee(id, worknum, name, sex, age, idcard, workaddress, entrydate)
values (1, '1','柳岩', '女', 20, '1234567890', '北京','2000-01-01'),
       (2, '2','张无忌', '男', 18, '0987654321', '北京','2005-09-01'),
       (3, '3','韦一笑', '男', 38, '1234567809', '上海','2005-08-01'),
       (4, '4','赵敏', '女', 18, '1234567777', '北京','2009-012-01'),
       (5, '5','小昭', '女', 16, '1234567899', '上海','2007-07-01'),
       (6, '6','杨逍', '男', 28, '123456789X', '北京','2006-06-01'),
       (7, '7','范瑶', '女', 40, '1234567888', '北京','2005-05-05'),
       (8, '8','黛绮丝', '女', 38, '1234566666', '天津','2015-05-30'),
       (9, '9','饭凉凉', '女', 46, '1111111111', '北京','2010-01-01'),
       (10, '10','陈友谅', '男', 55, '5555555555', '南京','2011-11-11'),
       (11, '11','张士诚', '男', 53, '3333333333', '江苏','2004-03-03'),
       (12, '12','常遇春', '男', 32, '7777777777', '北京','2007-07-07'),
       (13, '13','张三丰', '男', 99, '9999999999', '南阳','2009-09-09'),
       (14, '14','灭绝', '女', 80, '8888888888', '西安','2008-08-08'),
       (15, '15','胡青牛', '男', 33, '6666666666', '西安','2006-06-06'),
       (16, '16','周芷若', '女', 18, null, '北京','2012-12-01');


## 基本查询
select name,employee.worknum,age from employee;

select * from employee;

select distinct workaddress '工作地址' from employee;

select distinct employee.workaddress from employee;

## 条件查询
select * from employee where age = 99;
select *from employee where  age < 20;
select *from employee where  age <= 20;
select *from employee where idcard is null;
select *from employee where idcard is not null;
select * from employee where age != 99;
select * from employee where age <> 99;

select * from employee where age >= 15 && age <= 20;
select * from employee where age >= 15 and age <= 20;
select *from employee where age between 18 and 20;
select *from employee where sex = '女' && age < 25;

select *from employee where age = 18 or age = 20 or age = 40;
select *from employee where age in(18,20,40);
-- 查询姓名为两字符的员工
select *from employee where name like '__';
-- 查询身份证号最后一位是X的员工信息
select *from employee where idcard like '%X';
select *from employee where idcard like '_________X';

## 聚合函数
-- 统计企业员工数量
select count(*) from employee; -- 16
select count(idcard) from employee; -- 15
-- 平均年龄
select avg(employee.age) from employee;
-- 最大年龄
    select max(employee.age) from employee;
-- 最小年龄
select min(employee.age) from employee;
-- 北京年龄和
select sum(employee.age) from employee where workaddress = '北京';

## 分组查询
-- 根据性别分组,统计男性员工和女性员工
select sex, count(*) from employee group by sex;

-- 根据性别分组,统计男性员工和女性员工的平均年龄
select employee.sex, avg(employee.age) from employee group by sex;

-- 查询年龄小于45岁的员工, 并根据工作地址进行分组, 获取员工数量大于等于3的工作地址
select workaddress, count(*) from employee where age < 45 group by workaddress having count(*) >=3;

## 排序查询
-- 根据年龄对员工进行升序排序
select * from employee order by age asc;
select * from employee order by age;
-- 倒序
select * from employee order by age desc ;

-- 根据入职时间对员工进行降序排序
select * from employee order by entrydate desc ;

-- 根据年龄对公司员工进行升序排序, 年龄相同, 按入职时间降序排序
select *
from employee order by age, entrydate desc ;

## 分页查询
-- 查询第一页的员工数据, 每页10条记录
select *from employee limit 0,10;

-- 查询第二页员工数据, 每页10条记录
select *from employee limit 10,10;

### DQL_Practice
-- 查询年龄为20,21,22,23岁的女员工信息
select *from employee where sex = '女' and age in (20,21,22,23);

-- 查询性别为男, 并且年龄在20-40岁(含)的姓名为三个字的员工
select * from employee where sex = '男' && (age between 20 and 40) and name like '___';

-- 统计员工表中,年龄小于60岁的男性员工和女性员工的人数
select sex, count(*)from employee where age < 60 group by sex;

-- 查询所有年龄小于等于35岁的姓名和年龄,并查询结果按年龄生序排序,如果年龄按入职排序降序排序
select name,employee.age,entrydate from employee where age <= 35 order by age, entrydate desc;

-- 查询性别为男,并且年龄在20-40岁以内的前五个员工,对查询的结果按年龄升序排序,年龄相同按入职排序升序排序
select * from employee where sex = '男' and (age between 20 and 40) order by age asc,entrydate asc limit 5;


## 用户管理

-- 创建一个用户 itcast, 只能在当前逐渐localhost访问, 密码 123456
create user 'itcast'@'localhost' identified by '123456';

-- 创建用户 zj, 可以在任意主机访问该数据库, 密码 123456
create user  'zj'@'%' identified by '123456';

-- 修改用户 zj 的访问密码为 1234
alter user 'zj'@'%' identified with mysql_native_password by '1234';

-- 删除用户 itcast@localhost
drop user 'itcast'@'localhost';

-- 查询权限
show grants for 'zj'@'%';

-- 授予权限
grant all on itcast.* to 'zj'@'%';

-- 撤销权限
revoke all on itcast.* from 'zj'@'%';