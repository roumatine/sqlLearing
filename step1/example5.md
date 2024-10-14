# -- 多对多
create table student(
    id int auto_increment primary key comment '主键ID',
    name varchar(50) comment '姓名',
    no varchar(10) comment '学号'
) comment '学生表';
insert into student
values (null, '黛绮丝','2000100101'), (null, '谢逊','2000100102'),
       (null, '殷天正','2000100103'), (null, '殷天正','2000100104');

create table course(
    id int auto_increment primary key comment '主键ID',
    name varchar(10) comment '课程名称'
) comment '课程表';
insert into course values (null, 'Java'), (null, 'PHP'),(null, 'MySQL'),(null, 'Hadoop');

-- 中间表 --
create table student_course(
    id int auto_increment comment '主键' primary key,
    student_id int not null comment '学生ID',
    course_id int not null comment '课程ID',
    constraint fk_course_id foreign key (course_id) references course(id),
    constraint fk_student_id foreign key (student_id) references student(id)
) comment '学生课程中间表';
insert into student_course values (null,1,1),(null,1,2),(null,1,3),(null,2,2),(null,2,3),(null,3,4);

select * from student s, student_course sc, course c where s.id = sc.student_id and sc.course_id = c.id; -- 三表联查

# -- 一对一 --
create table tb_user(
    id int auto_increment primary key comment '主键ID',
    name varchar(10) comment '姓名',
    age int comment '年龄',
    gender char(1) comment '1:男 2:女',
    phone char(11) comment '手机号'
) comment '用户基本信息';

create table tb_user_edu(
    id int auto_increment primary key comment '主键ID',
    degree varchar(20) comment '学历',
    major varchar(50) comment '专业',
    primary_school varchar(50) comment '小学',
    middle_school varchar(50) comment '中学',
    university varchar(50) comment '大学',
    user_id int unique comment '用户ID',
    constraint fk_user_id foreign key (user_id) references tb_user(id)
) comment '用户教育信息';

insert into tb_user(id, name, age, gender, phone) values
(null,'黄渤',45,'1','18888881111'),
(null,'冰冰',35,'2','18300002222'),
(null,'码云',55,'1','18800088888'),
(null,'李彦宏',58,'1','18888889999');

insert into tb_user_edu(id, degree, major, primary_school, middle_school, university, user_id) values
    (null, '本科','舞蹈','静安区第一小学','静安区第一中学','北京舞蹈学院',1),
    (null, '硕士','表演','朝阳区第一小学','朝阳区第一中学','北京电影大学',2),
    (null, '本科','英语','杭州市第一小学','杭州市第一中学','杭州师范大学',3),
    (null, '本科','应用数学','阳泉第一小学','阳泉区第一中学','清华大学',4);


# 多表查询
create table dept(
    id int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '部门名称'
) comment '部门表';
insert into dept(id, name) values (1, '研发部'),(2, '市场部'),(3, '财务部'),(4, '销售部'),(5, '总经办'),(6, '人事部');

create table emp(
    id int auto_increment comment 'ID' primary key ,
    name varchar(50) not null comment '姓名',
    age int comment '年龄',
    job varchar(20) comment '职务',
    salary int comment '薪资',
    entrydate date comment '入职时间',
    managerid int comment '直属领导ID',
    dept_id int comment '部门ID'
) comment '员工表';
insert into emp(id, name, age, job, salary, entrydate, managerid, dept_id) values
    (1, '金庸', 66, '总裁', 20000, '2000-01-01',null,5),
    (2, '张无忌', 20, '项目经理', 12500, '2005-12-05',1,1),
    (3, '杨逍', 33, '开发', 8400, '2000-11-03',2,1),
    (4, '韦一笑', 48, '开发', 10000, '2002-02-07',2,1),
    (5, '常遇春', 43, '开发', 10500, '2000-09-07',3,1),
    (6, '小昭', 19, '程序员鼓励师', 6000, '2004-10-12',2,1),
    (7, '灭绝', 60, '财务总监', 8500, '2002-09-12',1,3),
    (8, '周芷若', 19, '会计', 4800, '2006-06-02',7,3),
    (9, '丁敏君', 23, '出纳', 5250, '2009-05-13',7,3),
    (10, '赵敏', 20, '市场总监', 12500, '2004-10-12',1,2),
    (11, '鹿杖客', 56, '职员', 3750, '2006-10-06',10,2),
    (12, '鹤笔翁', 19, '职员', 3750, '2007-05-09',10,2),
    (13, '方东白', 19, '职员', 5500, '2009-02-12',10,2),
    (14, '张三丰', 88, '销售总监', 14000, '2004-10-12',1,4),
    (15, '俞莲舟', 38, '销售', 4600, '2004-10-12',14,4),
    (16, '宋远桥', 40, '销售', 4600, '2004-10-12',14,4),
    (17, '陈友谅', 42, null, 2000, '2011-10-12',1,null);

alter table emp
add foreign key (dept_id) references dept(id);

## -- 多表查询 -- 笛卡尔积
select * from emp;
select * from emp, dept where emp.dept_id = dept.id;



## -- 内连接
-- 1.查询每个员工的姓名,及关联的部门名称,隐式内连接实现
-- 表结构： emp, dept
-- 连接条件 : emp.dept_id = dept.id
select emp.name,dept.name
from emp, dept where emp.dept_id = dept.id;

-- 表的别名 起了别名只能从别名来限定字段---->执行顺序 from优先
select e.name,d.name from emp e, dept d where e.dept_id = d.id;

-- 2. 查询每一个员工的姓名, 及关联的部门名称,显式内连接实现 --- INNER JOIN ... ON ...
-- 表结构： emp, dept
-- 连接条件 : emp.dept_id = dept.id
select e.name,d.name from emp e inner join dept d on e.dept_id = d.id;
select e.name,d.name from emp e join dept d on e.dept_id = d.id;


## -- 外连接
-- 1. 查询emp表的所有数据,和对应部门信息(左链接)
-- 表结构： emp, dept
-- 连接条件 : emp.dept_id = dept.id
select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;
select e.*, d.name from emp e left join dept d on e.dept_id = d.id;

-- 2. 查询dept表的所有数据,和对应员工信息(右链接)
-- 表结构： emp, dept
-- 连接条件 : emp.dept_id = dept.id
select d.*,e.* from emp e right join dept d on e.dept_id = d.id;

-- 改成左外
select d.*,e.* from dept d left join emp e on e.dept_id = d.id;


## -- 自连接
-- 1. 查询员工机器所属领导名字
-- 表结构: emp
select a.name, b.name from emp a, emp b where a.managerid = b.id;

-- 2. 查询所有员工emp 机器领导名字emp , 如果员工没有领导,也需要查询出来
-- 表结构: emp a, emp b
select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = b.id;



# -- 子查询
## -- 标量子查询
-- 1. 查询"销售部"的所有员工信息
-- a. 查询销售部部门id
    select id from dept where name = '销售部';
-- b. 根据销售部门id查询员工id
    select * from emp where dept_id = 4;
-- 合并为一条SQL
select * from emp where dept_id = (select id from dept where name = '销售部');

-- 2. 查询在"方东白"入职之后的员工信息
-- a. 查询方东白入职日期
 select emp.entrydate from emp where name = '方东白';
-- b. 查询指定入职日期后入职员工信息
 select * from emp where entrydate > '2009-02-12';
-- 合并
 select * from emp where entrydate > (select emp.entrydate from emp where name = '方东白');


## -- 列子查询
-- 1. 查询 销售部 和 市场部 的所有成员信息
-- a. 查询 销售部 和 市场部 的部门id
select id from dept where name = '销售部' or name = '市场部';
-- b. 根据部门id查询员工信息
select * from emp where dept_id in(2,4);
-- 合并
select * from emp where dept_id in (select id from dept where name = '销售部' or name = '市场部');

-- 2. 查询比 财务部 所有人工资都高的员工信息
-- a. 查询所有财务部人员工资
select id from dept where name = '财务部'; -- id = 3;
select emp.salary from emp where dept_id = 3;
-- 合并
select emp.salary from emp where dept_id = (select id from dept where name = '财务部');
-- b. 比 财务部 所有人工资都高的信息
select * from emp where salary > all(select emp.salary from emp where dept_id = (select id from dept where name = '财务部'));

-- 3.查询比研发部任意一人工资高的员工信息
-- a. 研发部所有人工资
select emp.salary from emp where dept_id = (select id from dept where name = '研发部');
-- b. 比研发部任意一人工资高的员工信息
select * from emp where salary > any (select emp.salary from emp where dept_id = (select id from dept where name = '研发部'));
select * from emp where salary > some (select emp.salary from emp where dept_id = (select id from dept where name = '研发部'));


## -- 行子查询
-- 1. 查询与 张无忌 薪资和直属领导相同的员工信息

-- a. 查询张无忌的薪资和直属领导
select salary, managerid from emp where name = '张无忌';
-- b. 查询与 张无忌 薪资和直属领导相同的员工信息
select * from emp where salary = 12500 and managerid = 1;
select * from emp where (salary, managerid) = (12500, 1);
-- ---->
select * from emp where (salary, managerid) = (select salary, managerid from emp where name = '张无忌');


## -- 表子查询
-- 1. 查询与 鹿杖客 ,宋远桥 职位和薪资相同的员工信息
-- a. 查询查询与 鹿杖客 ,宋远桥 只为和薪资
    select job, salary from emp where name = '鹿杖客' or name = '宋远桥';
-- b. 相同的员工信息
select * from emp where (job, salary) in
        (select job, salary from emp where name = '鹿杖客' or name = '宋远桥');


-- 2. 查询入职日期是 2006-01-01 之后的员工信息,及其部门信息
-- a. 查询入职日期是 2006-01-01 之后的员工信息
    select * from emp where entrydate > '2006-01-01';
-- b. 部门信息 以a为表进行子查询
select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e
    left join dept d on e.dept_id = d.id;


### · 多表查询案例
create table salgrade(
    grade int,
    losal int,
    hisal int
) comment '薪资等级表';
insert into salgrade values (1, 0, 3000);
insert into salgrade values (2, 3001, 5000);
insert into salgrade values (3, 5001, 8000);
insert into salgrade values (4, 8001, 10000);
insert into salgrade values (5, 10001, 15000);
insert into salgrade values (6, 15001, 20000);
insert into salgrade values (7, 20001, 25000);
insert into salgrade values (8, 25001, 30000);

-- 1. 查询员工的姓名、年龄、职位、部门信息(隐式内连接)
-- table : emp, dept
-- connect : emp.dept_id = dept.id
select e.name,e.age,e.job,d.name from emp e, dept d where e.dept_id = d.id;

-- 2. 查询年龄小于30岁的员工姓名、年龄、职位、部门信息(显示内连接)
-- table : emp, dept
-- connect : emp.dept_id = dept.id
select e.name,e.age,e.job,d.name from emp e inner join dept d on e.dept_id = d.id where e.age < 30;

-- 3. 查询拥有员工的部门ID、部门信息
select distinct d.id, d.name from emp e, dept d where e.dept_id = d.id;

-- 4. 查询所有年龄大于40岁的员工,及其归属的部门名称; 如没有分配部门,也需要展示出来(外连接包含完全数据)
-- 外连接
select e.*, d.name from emp e left join dept d on d.id = e.dept_id where e.age > 40;

-- 5. 查询所有员工的工资等级
-- table : emp, salgrade
-- 需要判定 sal 等级
-- connect emp.salary >= salgrade.losal and emp.salary<= salgrade.hisal
select e.*,s.grade -- ,s.losal,s.hisal
from emp e, salgrade s where e.salary >= s.losal and e.salary <=s.hisal;

select e.*,s.grade
from emp e, salgrade s where e.salary between s.losal and s.hisal;

-- 6. 查询" 研发部 "所有员工的信息及工资等级
-- table : emp, dept, salgrade
-- connect : 1.connect emp.salary between salgrade.losal and emp.salgrade.hisal
--          2. emp.dept_id = dept.id
-- 查询条件: dept.name = '研发部'
-- (连接n张表则会有n-1个连接条件)
select e.*, s.grade
from emp e,
     dept d,
     salgrade s
where e.dept_id = d.id
  and e.salary between s.losal and s.hisal
  and d.name = '研发部';

-- 7. 查询" 研发部 "员工的平均工资
-- table : emp, dept
-- connect : emp.dept_id = dept.id
select avg(e.salary)
from emp e, dept d where e.dept_id = d.id and d.name = '研发部';

-- 8. 查询工资比" 灭绝 "高的员工信息
-- a. 查询灭绝薪资
select salary
from emp
where name = '灭绝';
-- b. 比他工资高的员工信息
select *
from emp where salary > 8000;
-- 子查询
select *
from emp
where salary > (select salary
                from emp
                where name = '灭绝');

-- 9. 查询比平均薪资高的员工信息
-- a. 平均工资
select avg(salary) from emp;
-- b. 查询高于...
select *
from emp where salary > (select avg(salary) from emp);

-- 10. 查询低于本部门平均工资的员工信息
-- a. 查询指定部门的平均薪资 1
select avg(e1.salary) from emp e1 where e1.dept_id = 1;
select avg(e1.salary) from emp e1 where e1.dept_id = 2;

-- b.
select *, (select avg(e1.salary) from emp e1 where e1.dept_id = e2.dept_id) '平均'
from emp e2
where e2.salary < (select avg(e1.salary) from emp e1 where e1.dept_id = e2.dept_id);

-- 11. 查询所哟的部门信息, 并统计部门的员工人数
select id,name from dept;
select count(*) from emp where dept_id = 1;
-- --->
select id,name, id '人数' from dept;
select d.id,d.name, (select count(*) from emp e where e.dept_id = d.id ) '人数' from dept d;

-- 12. 查询所有学生的选课情况,展示出学生名称,学号,课程名称
-- table : student, student_course, course
-- connect : student.id = student_course_id , course_id =student_course_courseid
select s.name, s.no, c.name
from student s,
     student_course sc,
     course c
where s.id = sc.student_id
  and sc.course_id = c.id;
