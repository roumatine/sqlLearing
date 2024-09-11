1. 根据需求，完成表结构创建
字段名 字段含义 字段类型 约束条件            约束关键字
id    ID唯一标识int    主键,并自动增长      PRIMARY KEY, AUTO_INCREMENT
name  姓名   varchar(10)不为空, 并且唯一    NOT NULL, UNIQUE
age   年龄   int      大于0, 并且小于120    CHECK
status状态   char(1)  如果没有指定,默认为1   DEFAULT
gender性别   char(1)  无


create table usr(
    id tinyint primary key auto_increment comment '主键id',
    name varchar(10) not null unique comment '姓名',
    age int check ( age > 0 && age <= 120 ) comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别'
) comment '用户表';
-- 插入数据
insert into usr(name, age, status, gender) values ('Tom1', 19, '1', '男'), ('Tom2', 25, '0', '男');
insert into usr(name, age, status, gender) values ('Tom3', 19, '1', '男');

insert into usr(name, age, status, gender) values (null, 19, '1', '男');
insert into usr(name, age, status, gender) values ('Tom4', 80, '1', '男');
insert into usr(name, age, status, gender) values ('Tom5', -1, '1', '男');
insert into usr(name, age, status, gender) values ('Tom5', 121, '1', '男');

insert into usr(name, age, gender) values ('Tom5', 120, '男');


# 外键约束
create table dept(
    id int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '部门名称'
) comment '部门表';

insert into dept(id, name) values (1, '研发部'),(2, '市场部'),(3, '财务部'),(4, '销售部'),(5, '总经办');

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
    (6, '小昭', 19, '程序员鼓励师', 6000, '2004-10-12',2,1);

-- 添加外键
alter table emp
add foreign key (dept_id) references dept(id);

-- 删除外键
alter table emp drop foreign key fk_emp_dept_id;

-- 外键删除和更新行为 删除1pt
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade ;
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update set null on delete set null ;
