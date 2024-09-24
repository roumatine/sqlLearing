# 索引

## 索引语法

use itcast;
create table tb_user(
    id int auto_increment primary key,
    name varchar(10),
    phone varchar(20),
    email varchar(50),
    profession varchar(20),
    age int,
    gender char(1),
    status char(1),
    createtime date
);
insert into tb_user(id, name, phone, email, profession, age, gender, status, createtime)
values (1,'吕布','1779990000','lvbu666@163.com','软件工程', 23, 1, 6, '2001-02-02'),
       (2,'曹操','1779990001','caocao666@qq.com','通讯工程',33, 1, 0, '2001-03-05'),
       (3,'赵云','1779990002','17799990@139.com','英语', 34, 1, 2, '2002-03-02'),
       (4,'孙悟空','1779990003','17799990@sina.com','工程造价', 54, 1, 0, '2001-07-02'),
       (5,'花木兰','1779990004','19980729@sina.com','软件工程',23, 2, 0, '2001-04-22'),
       (6,'大桥','1779990005','daqiao666@sina.com','舞蹈',22, 2, 0, '2001-02-07'),
       (7,'露娜','1779990006','luna_love@sina.com','应用数学',24, 2, 0, '2001-02-08'),
       (8,'程咬金','1779990007','chengyaojin@163.com','化工',38, 1, 5, '2001-05-23'),
       (9,'项羽','1779990008','xiangyu666@qq.com','金属材料',43, 1, 0, '2001-09-18'),
       (10,'白起','1779990009','baiqi666@sina.com','机械工程及其自动化',27, 1, 2, '2001-08-16'),
       (11,'韩信','1779990010','hanxin520@163.com','无机非金属材料工程',27, 1, 0, '2001-06-12'),
       (12,'阿珂','1779990011','jingke123@163.com','会计',29, 1, 0, '2001-05-11'),
       (13,'兰陵王','1779990012','lanlingwang666@126.com','工程造价',44, 1, 1, '2001-04-09'),
       (14,'狂铁','1779990013','kuangtie@sina.com','应用数学',43, 1, 2, '2001-04-10'),
       (15,'貂蝉','1779990014','84958948375@qq.com','软件工程',40, 2, 3, '2001-02-12'),
       (16,'妲己','1779990015','2783238293@qq.com','软件工程',31, 2, 0, '2001-01-30'),
       (17,'芈月','1779990016','xiaomin2001@sina.com','工业经济',35, 2, 0, '2001-05-03'),
       (18,'嬴政','1779990017','8839434342@qq.com','化工',38, 1, 1, '2001-08-08'),
       (19,'狄仁杰','1779990018','jujiamlm8166@163.com','国际贸易',30, 1, 0, '2007-03-12'),
       (20,'安其拉','1779990019','jdodmlh@126.com','城市规划',51, 2, 0, '2001-08-15'),
       (21,'典韦','1779990020','ycaunanjian@163.com','城市规划',52, 1, 2, '2001-04-12'),
       (22,'廉颇','1779990021','lianpo321@126.com','土木工程',19, 1, 3, '2002-07-18'),
       (23,'后裔','1779990022','altycj2000@139.com','城市园林',20, 1, 0, '2002-03-10'),
       (24,'姜子牙','1779990023','37483844@qq.com','工程造价',29, 1, 4, '2003-05-26');

-- 1.name字段为姓名字段,该字段的值可能会重复,为该字段创建索引
    show index from tb_user; -- \G 展示索引
    create index idx_user_name on tb_user(name);
-- 2.phone手机号字段的值是非空,且唯一的,为该字段创建唯一索引
    create unique index idx_user_phone on tb_user(phone);
-- 3.为profession、age、status创建联合索引
    create index idx_user_pro_age_sta on tb_user(profession,age,status);
-- 4.为email建立合适的索引来提升查询效率
    create index idx_user_email on tb_user(email);


### explain
使用数据库自step1中获取
-- 查询需求：查询所有学生的选课记录
explain select s.*,c.* from student s, course c, student_course sc where s.id = sc.student_id and c.id = sc.course_id;


-- 查询需求:MySQL课程的学生
explain select * from student s where s.id in(select student_id from student_course sc where sc.course_id = (select id from course c where c.name = 'MySQL'));


思考:
一张表有四个字段(id,username,password,status),由于数据量大,需要对一下SQL语句进行优化,如何进行才是最优方案:
1. select id,username,password from tb_user where username = 'itcast';
针对username和password进行联合索引


###  前缀索引 ### 
select * from tb_user;
select count(*) from tb_user;
-- select count(email) from tb_user;
select count(distinct email) from tb_user;
select count(distinct email) / count(*) from tb_user;
select count(distinct substring(email, 1, 9)) / count(*) from tb_user;

show index from tb_user;
create index idx_email_5 on tb_user(email(5));
explain select * from tb_user where email = '19980729@sina.com';

### 单列索引与联合索引 ###
