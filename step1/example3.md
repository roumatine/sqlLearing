# -- 字符串函数
-- concat
select concat('hello','mysql');
-- lower
select lower('HELLOW');
-- upper
select upper('hello');
-- lpad
select lpad('01',5,'-');
-- rpad
select rpad('01',5,'-');
-- trim
select trim('   hello mysel  ');
-- substring
select substring('hello mysql', 1, 8);

-- 由于业务需求的变更, 企业员工的工号统一为5位数, 目前不足5位数的在前面补0，比如1号00001
update itcase.employee set worknum = lpad(employee.worknum, 5, '0');

# -- 数值函数
-- ceil
select ceil(1.5);
select ceil(1.1);
-- floor
select floor(2.9);
-- mod
select mod(3,6);
select mod(6,4);
-- rand
select rand();
-- round
select round(2.3655,2);

-- 通过数据库函数,生成一个六位数的随机验证码
select lpad(round(rand()*1000000,0), 6, '0');

# -- 日期函数
-- curdate
select curdate();
-- curtime
select curtime();
-- now
select now();

-- year, month, day
select year(now());
select month(now());
select day(now());
-- date_add
select date_add(now(), interval 70 year); -- year->month->day
-- datediff
select datediff('2021-12-01', '2021-11-01');

-- 查询所有员工入职天数,并根据入职天数倒序排序
select name, datediff(curdate(), entrydate) as 'entrydays' from employee order by entrydays;

# -- 流程函数
-- if
select if(true, 'ok', 'error');
select if(false, 'ok', 'error');
-- ifnull
select ifnull('ok','Default');
select ifnull('','Default'); -- 返回空串
select ifnull(null,'Default'); -- 返回Default

-- case when else end
-- 需求: 查询emp列表的员工姓名和工作地址(上海/北京---> 一线城市, 其他--->二线城市)
select
    name,
    (case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end) as '工作地址'
from employee;

-- 统计班级各个成员的成绩,展示规则如下
-- >= 85 优秀
-- >= 60 及格
-- else 不及格
create table score(
    id int comment 'ID',
    name varchar(20) comment 'name',
    math int comment '数学',
    english int comment '英语',
    chinese int comment '语文'
) comment '学员成绩表';
insert into score(
    id, name, math, english, chinese)
values (1, 'Tom', 67,88,95), (2, 'Rose', 23, 66, 90), (3, 'Jack', 56,98,76);

select
    id,
    name,
    (case when math >=85 then '优秀' when math >=60 then '及格' else '不及格' end) as '数学',
    (case when english >=85 then '优秀' when english >=60 then '及格' else '不及格' end) as '英语',
    (case when chinese >=85 then '优秀' when chinese >=60 then '及格' else '不及格' end) as '语文'
from score;