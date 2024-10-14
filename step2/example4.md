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