# 视图
-- 创建视图
create or replace view stu_v_1 as select id,name from student where id < 10;

-- 查询视图
show create view stu_v_1;
select * from stu_v_1;
select * from stu_v_1 where id < 3;

-- 修改视图
create or replace view stu_v_1 as select id,name,no from student where id < 10;
alter view stu_v_1 as select id,name from student where id < 10;

-- 删除视图
drop view if exists stu_v_1;

-- 检查选项
create or replace view stu_v_1 as select id,name from student where id <=20 with cascaded check option ; -- cascaded/local

select * from stu_v_1;
insert into stu_v_1 values (6,'Tom'); -- 插入了student表
insert into stu_v_1 values (30,'Tom'); -- 该视图差不到但是插入表中,所以要加入with cascaded check option检查选项

-- cascaded 
create or replace view stu_v_1 as select id,name from student where id <=15;

insert into stu_v_1 values (5,'Tom');
insert into stu_v_1 values (16,'Tom');

create or replace view stu_v_2 as select id,name from stu_v_1 where id >=10 with cascaded check option ;

insert into stu_v_2 values (13,'Tom');
insert into stu_v_2 values (17,'Tom');

create or replace view stu_v_3 as select id,name from stu_v_2 where id <20;

insert into stu_v_3 values (14,'Tom');


-- local
create or replace view stu_v_4 as select id,name from student where id <=15;

insert into stu_v_4 values (5,'Tom');
insert into stu_v_4 values (16,'Tom'); -- 不对条件检查

create or replace view stu_v_5 as select id,name from stu_v_4 where id >=10 with local check option ;

insert into stu_v_5 values (13,'Tom');
insert into stu_v_5 values (17,'Tom'); -- v4不检查

create or replace view stu_v_6 as select id,name from stu_v_5 where id <20;

insert into stu_v_6 values (14,'Tom');

## 视图案例

-- 1.为了包子数据库表的安全性，开发人员在操作tb_user表时，只能看到用户的基本字段，屏蔽手机号和邮箱两个字段。
create view tb_user_view as select id,name,profession,gender,status,createtime from tb_user;

select * from tb_user_view;

-- 2. 查询每个学生所选修的课程(三张表联查)，这个功能在很多的业务中都有使用到，为了简化操作定义一个视图

-- 三表联查
select s.name,s.no,c.name from student s, student_course sc, course c where s.id = sc.student_id and sc.course_id = c.id;

create view tb_user_course_view as select s.name,s.no,c.name from student s, student_course sc, course c where s.id = sc.student_id and sc.course_id = c.id; -- [42S21][1060] Duplicate column name 'name'

create view tb_user_course_view as select s.name student_name,s.no student_no,c.name course_name from student s, student_course sc, course c where s.id = sc.student_id and sc.course_id = c.id;

select * from tb_user_course_view;


# 存储过程
-- 存储过程基本语法
-- 创建
create procedure p1()
begin
    select count(*) from student; -- 在命令行中会报错，分号会表示语句结束
end;

-- 调用
call p1(); -- 执行了select count(*) from student;

-- 查看
select * from  information_schema.ROUTINES where ROUTINE_SCHEMA = 'test';

show create procedure p1;

-- 删除
drop procedure if exists p1;


delimiter $$ -- 设置以两个$符号作为SQL语句结束
create procedure p1()
begin
    select count(*) from student;
end$$
delimiter ; -- 修改回来


-- 系统变量
-- 查看系统变量
SHOW variables ;

show session variables like 'auto%';

show global variables like 'auto%';

select @@autocommit;


-- 设置系统变量

set session autocommit = 0; -- 当前会话为0

insert into course(id,name) values (5,'origin');
commit ;

set session autocommit = 1;
set global autocommit = 0; -- 服务器重启后会重制为默认值

-- 用户自定义变量
-- 赋值
set @myname = 'itcast';
set @MYAGE := 10;
set @mygender := '男', @myhobby := 'java';

set @Mycolor := 'red';

select count(*) into @mycount from tb_user;

-- 使用
select @myname,@MYAGE,@mygender,@myhobby;

select  @Mycolor,@mycount;

select @abc;

-- 声明
-- 赋值
create procedure p2()
begin
    declare stu_count int default 0;
    -- set stu_count := 100;
    select count(*) into stu_count from student;
    select stu_count;
end;

call p2();


-- if
-- 根据定义的分数score变量判断当前分数对应的分数等级
-- 1.score >=85,等级为优秀
-- 2.score >=60且score<85,等级为合格
-- 3.score <60 等级为不及格
create procedure p3()
begin
    declare score int default 58;
    declare result varchar(10);

    if score >= 85 then
        set result := '优秀';
    elseif score >=60 then
        set result := '及格';
    else
        set result := '不及格';
    end if;
    select result;
end;

call p3();


create procedure p4(in score int,out result varchar(10))
begin
    if score >= 85 then
        set result := '优秀';
    elseif score >=60 then
        set result := '及格';
    else
        set result := '不及格';
    end if;
end;

call p4(98, @result);
select @result;

-- 2.将传入(IN)的200分制的分数进行换算，换算成百分制然后返回(OUT) ---> inout
create procedure p5(inout score double)
begin
    set score := score * 0.5;
end;

set @score = 198;
call p5(@score);
select @score;


-- case
-- 根据传入月份，判断月份所属的季节(要求采用case结构)
-- 1. 1-3 月份为第一季度
-- 2. 4-6 月份为第二季度
-- 3. 7-9 月份为第三季度
-- 4. 10-12 月份为第四季度

create procedure p6(in month int)
begin
    declare result varchar(10);

    case
        when month >=1 and month <=3 then
            set result := '第一季度';
        when month >=4 and month <=6 then
            set result := '第二季度';
        when month >=7 and month <=9 then
            set result := '第三季度';
        when month >=10 and month <=12 then
            set result := '第四季度';
        else
            set result := '非法参数';
    end case;

    select concat('您输入的月份为:', month, ',所属的季度为', result);
end;

call p6(15);


-- while
-- 计算从1累加到n的值，n为传入的参数
-- A. 定义一个局部变量，记录累加后情况
-- B. 每循环一次豆会对n进行减1，如果减到0则退出循环

create procedure p7(in n int)
begin
    declare total int default 0;
    while n > 0 do
        set total := total + n;
        set n := n - 1;
    end while;
    select total;
end;

call p7(10);

-- repeat
-- 计算从1累加到n的值，n为传入的参数

create procedure p8(in n int)
begin
    declare total int default 0;
    repeat
        set total := total + n;
        set n := n - 1;
    until n <= 0
    end repeat;
    select total;
end;


call p8(100);


-- loop
-- 1. 计算从1累加到n的值，n为传入的参数
-- LEAVE 退出循环
create procedure p9(in n int)
begin
    declare total int default 0;

    sum:loop
        if n <= 0 then
            leave sum;
        end if;

        set total := total + n;
        set n := n - 1;
    end loop sum;

    select total;
end;

call p9(100);

-- 2. 计算从1到n之间偶数累加的值，n为传入的参数
-- 当此累加数据是奇数，则进入下一次循环 --->iterate

create procedure p10(in n int)
begin
    declare total int default 0;

    sum:loop
        if n <= 0 then
            leave sum;
        end if;

        if n % 2 = 1 then
            set n := n - 1;
            iterate sum;
        end if;

        set total := total + n;
        set n := n - 1;
    end loop sum;

    select total;
end;

call p10(100);

-- 1.游标
-- 根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名(name)和专业(profession)
-- 并将用户的姓名和专业插入到所创建的一张新表中(id,name,profession)

-- 逻辑
-- A.  声明游标，存储查询结果集
-- B. 准备：创建表结构
-- C. 开启游标
-- D. 获取游标中的记录
-- E. 插入数据到新表中
-- F：关闭游标
-- 未加入条件处理会有报错 [02000][1329] No data - zero rows fetched, selected, or processed
-- 2.条件处理程序

create procedure p11(in uage int)
begin
    declare u_name varchar(100);
    declare u_pro varchar(100);
    declare u_cursor cursor for select name,profession from tb_user where age <= uage;
    declare exit handler for SQLSTATE '02000' close u_cursor; -- 声明条件处理程序，当sql 02000时候触发
    -- 状态码可以从mysql官方文档查询


    drop table if exists tb_user_pro;
    create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );

    open u_cursor;
    while true do
        fetch u_cursor into u_name,u_pro;
        insert into tb_user_pro values (null, u_name,u_pro);
    end while;

    close u_cursor;
end;

call p11(30);


create procedure p12(in uage int)
begin
    declare u_name varchar(100);
    declare u_pro varchar(100);
    declare u_cursor cursor for select name,profession from tb_user where age <= uage;
    declare exit handler for NOT FOUND close u_cursor;


    drop table if exists tb_user_pro;
    create table if not exists tb_user_pro(
        id int primary key auto_increment,
        name varchar(100),
        profession varchar(100)
    );

    open u_cursor;
    while true do
        fetch u_cursor into u_name,u_pro;
        insert into tb_user_pro values (null, u_name,u_pro);
    end while;

    close u_cursor;
end;

call p12(40);

# 存储函数
-- 存储函数
-- 计算从1累加到n的值，n为传入的参数

create function fun1(n int) -- 默认且只能为IN
returns int deterministic
begin
    declare total int default 0;

    while n > 0 do
        set total := total + n;
        set n := n - 1;
    end while;

    return total;
end;


select fun1(100);


# 触发器
-- 触发器
-- 通关触发器记录tb_user表的数据变更记录，将变更日志插入到日志表 user_log 中，包含增加修改删除

-- 准备工作: 日志表user_log
create table user_log(
    id int(11) not null auto_increment,
    operation varchar(20) not null comment '操作类型, insert/update/delete',
    operate_time datetime not null comment '操作时间',
    operate_id int(11) not null comment '操作ID',
    operate_params varchar(500) comment '操作参数',
    primary key (`id`)
);

-- 插入数据触发器
create trigger tb_user_insert_trigger
    after insert on tb_user for each row
begin
    insert into user_log(id, operation, operate_time, operate_id, operate_params) values
    (null, 'insert', now(), new.id, concat('插入数据内容为: id = ',new.id,', name = ',new.name,', phone = ',new.phone,', email = ',new.email,', profession = ',new.profession));
end;

-- 查看
show triggers ;

-- 删除
drop trigger tb_user_insert_trigger;


-- 插入数据到tb_user
insert into tb_user(id, name, phone, email, profession, age, gender, status, createtime)
values (25,'二皇子','18809091212','erhuangzi@163.com','软件工程',23,'1','1',now());


-- 修改数据触发器
create trigger tb_user_update_trigger
    after update on tb_user for each row
begin
    insert into user_log(id, operation, operate_time, operate_id, operate_params) values
    (null, 'update', now(), new.id, concat('更新之前的数据内容为: id = ',old.id,', name = ',old.name,', phone = ',old.phone,', email = ',old.email,', profession = ',old.profession,
                                           ' | 更新之后的数据内容为: id = ',new.id,', name = ',new.name,', phone = ',new.phone,', email = ',new.email,', profession = ',new.profession));
end;

-- 更新tb_user的一组数据
update tb_user set age = 25 where id = 25;


-- 删除数据触发器
create trigger tb_user_delete_trigger
    after delete on tb_user for each row
begin
    insert into user_log(id, operation, operate_time, operate_id, operate_params) values
    (null, 'delete', now(), old.id, concat('删除之前的数据内容为: id = ',old.id,', name = ',old.name,', phone = ',old.phone,', email = ',old.email,', profession = ',old.profession));
end;

-- 删除tb_user的一组数据
delete from tb_user where id = 25;