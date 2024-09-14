# Connection from WSL2 mysql to Datagrip

## 启动MySQL服务
`systemctl start mysqld`
`systemctl restart mysqld`
`systemctl stop mysqld`

/etc/init.d/mysql start # 启动
/etc/init.d/mysql stop # 停止
/etc/init.d/mysql status # 查看状态

## 查询自动生成的root用户密码
`grep 'temporary password' /var/log/mysqld.log`

## 修改密码复杂度
`set global validata_password.policy = 0;`
`set global validata_password.length = 4;`

## 下载MySQL
1. 使用管理员权限
` sudo -s `
2. 安装vim
`apt-install vim`
3. 安装ifconfig
`apt install net-tools`
4. 安装 MySQL
`sudo apt install mysql-server`
5. linux界面下修改MySQL监听地址 
`sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf`
`找到bind-address 和 mysqlx-bind-address 修改为 0.0.0.0, 修改完按键esc退出,输入:wq保存退出`
6. 默认的root用户只能当前节点localhost访问,是无法远程访问的,无密需要创建一个root账户用于远程访问
`mysql> create user 'root'@'%' identified with mysql_native_password by '1234';`
7. 设置权限
`sudo mysql -u root -p # 进入MySQL`
`mysql> use mysql; # 切换数据库`
`mysql> UPDATE user SET host ='%' WHERE user='root'; # 允许远程访问`
`mysql> grant all on *.* to 'root'@'%'; 给root用户分配权限`
`mysql> FLUSH PRIVILEGES; # 刷新权限`
`mysql> quit; # 退出`
再看看能否链接,如果任然报错可以不用root，自己建一个新用户在链接

## 建一个新用户
root下 输入mysql 无密码模式进入MySQL
1. `show databases;`
并为演示链接虚拟机MySQL建库标记: `create database test;`
2. 创建用户
`mysql> CREATE USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY 'test';`
3. 授权
`mysql> GRANT ALL PRIVILEGES ON *.* TO 'test'@'%';`
4. 重启MySQL
`service mysql restart`

## 查询ip地址
`fconfig 或 ifconfig | grep inet`