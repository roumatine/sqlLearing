# SQL优化
## 插入数据
### 大批量插入数据


### order by优化

没有创建索引时，根据age，phone进行排序
explain select id,age,phone from tb_user order by age,phone;

创建索引
create index id_user_age_phone_aa on tb_user(age,phone);

创建索引后，根据age,phone进行升序排列
explain select id,age,phone from tb_user order by age,phone;

创建索引后，根据age,phone进行降序排列
explain select id,age,phone from tb_user order by age desc,phone desc;

根据age,phone进行降序一个升序一个降序
explain select id,age,phone from tb_user order by age asc,phone desc;

创建索引
create index id_user_age_phone_ad on tb_user(age asc,phone desc);

根据age,phone进行降序一个升序一个降序
explain select id,age,phone from tb_user order by age asc,phone desc;

![asc_desc](/step2/pic/asc_desc.png)

### group by优化
删除掉目前的联合索引 idx_user_pro_age_sta
drop index idx_user_pro_age_sta on tb_user;

执行分组操作，根据profession字段分组
exlain select profession,count(*) from tb_user group by profession;

创建索引
create index idx_user_pro_age_sta on tb_user(profession,age,status);

执行分组操作，根据profession字段分组
exlain select profession,count(*) from tb_user group by profession;

执行分组操作，根据profession字段分组
exlain select profession,count(*) from tb_user group by profession,age;

explain select age,count(*) from tb_user where profession = '软件工程' group by age;

### limit优化
select * from tb_sku limit 2000010,10;
覆盖索引
select id from tb_sku order by id limit 2000010,10;
<!-- select * from tb_sku where id in(select id from tb_sku order by id limit 2000010,10); 无法在子查询中使用limit-->
select s.* from tb_sku s, (select id from tb_sku order by id limit 2000010,10) a where s.id = a.id;

### count优化
count用法

select * from tb_user;
select count(*) from tb_user;
select count(id) from tb_user;
update tb_user set profession = NULL where id = 24;
select count(tb_user.profession) from tb_user;
select count(1) from tb_user;

### update优化