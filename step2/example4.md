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
