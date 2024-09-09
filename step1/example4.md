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


2. 外键约束