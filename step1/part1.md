## 启动与停止 ##

Win + R: service.msc
找到MySQL80服务

命令行(管理员身份)
net start mysql80
net stop mysql80

## 客户端连接 ##

1. MySQL提供的客户端命令行工具
    MySQL Command Line Client
2. 系统自带命令行工具执行指令(配置环境变量)
    mysql -u root -p

## 数据模型 ##
客户端 -> DBMS(创建/操作数据库) -> 数据库(多个) -> 表结构

关系型数据库 :关系模型->二维表