# Ubuntu 20.04 搭建 LAMP 环境

为渗透测试准备LAMP环境,或者用该环境部署web服务,LAMP=Linux+Apache+Mysql+Php

本文Linux采用的是Ubuntu 20.04版本

## 更新

```shell
#更新源
sudo apt-get update
#更新软件
sudo apt-get upgrade
#更新系统软件
sudo apt-get dist-upgrade
```

## 部署Linux

虚拟机安装部署Ubuntu 20.04系统,基础知识,不多介绍.

## 部署Apache2

```shell
# 安装
sudo apt install apache2 -y
# 检查是否运行
sudo systemctl status apache2
# 加入自启动
sudo systemctl enable apache2
```

apache2默认启用80端口,在浏览器直接访问IP,出现`Apache2 Ubuntu Default Page`表示安装成功

## 部署MySQL

```shell
# 安装
sudo apt install mysql-server mysql-client
# 查看
sudo mysql
# 初始化MySQL配置
sudo mysql_secure_installation
#1
VALIDATE PASSWORD PLUGIN can be used to test passwords...
Press y|Y for Yes, any other key for No: N (选择N ,不会进行密码的强校验)

#2
Please set the password for root here...
New password: (输入密码)
Re-enter new password: (重复输入)

#3
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them...
Remove anonymous users? (Press y|Y for Yes, any other key for No) : N (选择N，不删除匿名用户)

#4
Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network...
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : N (选择N，允许root远程连接)

#5
By default, MySQL comes with a database named 'test' that
anyone can access...
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : N (选择N，不删除test数据库)

#6
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y (选择Y，修改权限立即生效)

# 配置远程访问
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf #找到 bind-address 修改值为 0.0.0.0(如果需要远程访问)
# 重启mysql
sudo /etc/init.d/mysql restart

# 登录
sudo mysql -u root -p
# 输入密码
# 查看数据库
mysql> show databases;
# 选择进入某数据库
mysql> use DATABASE_NAME;
# 查看数据表
mysql> show tables;

# 实例: 查看MySQL用户信息
# 切换数据库
mysql> use mysql;
#查询用户表信息
mysql> select host,user,plugin from user;
#设置权限与密码,老版本MySQL
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; #使用mysql_native_password修改加密规则
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '密码' PASSWORD EXPIRE NEVER; #更新一下用户的密码
mysql> UPDATE user SET host = '%' WHERE user = 'root'; #允许远程访问

#刷新cache中配置 刷新权限,如果无法更改密码使用flush privileges;然后再进行更改密码，修改加密规则操作。
mysql> flush privileges;
mysql> quit;

# 修改账户密码,以root为例,新版MySQL8.0
mysql> alter user 'root'@'%' identified with mysql_native_password by '密码';
# mysql8和原来的版本有点不一样，8的安全级别更高，所以在创建远程连接用户的时候，不能用原来的命令（同时创建用户和赋权）:
# 必须先创建用户（密码规则：mysql8.0以上密码策略限制必须要大小写加数字特殊符号）
mysql> CREATE USER 'sammy'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
#赋权
mysql> GRANT ALL PRIVILEGES ON *.* TO 'sammy'@'%' WITH GRANT OPTION;
```

## MySQL数据库操作相关命令

上面已经安装好MySQL,下面是对相关操作进行总结,如果仅需要部署LAMP环境,直接跳到部署PHP即可.

### mysql服务操作

```shell
# 1.启动mysql服务
[root@kali ~ ]# systemctl start mysql
# 2.停止mysql服务
[root@kali ~]# systemctl stop mysql
# 3.重启mysql服务
[root@kali ~]# systemctl restart mysql
# 4.进入mysql数据库
[root@kali ~]# mysql -u root -p
# 5.查看MySQL数据库信息
mysql> status;
# 6.退出mysql
mysql> quit;
or
mysql> exit;
# 7.更改密码 ：mysqladmin -u用户名 -p旧密码 password 新密码
mysql> mysqladmin -uroot -p123 password 123456
# 8.增加新用户 :grant select on 数据库.* to 用户名@登录主机 identified by “密码”
mysql> grant all privileges on *.* to root@"%" identified by "pwd" with grant option;
# 增加一个用户test2密码为abc,让他只可以在localhost上登录，并可以对数据库mydb进行查询、插入、修改、删除的操作 （localhost指本地主机，即MYSQL数据库所在的那台主机），这样用户即使用知道test2的密码，他也无法从internet上直接访问数据 库，只能通过MYSQL主机上的web页来访问了。
mysql> grant select,insert,update,delete on mydb.* to test2@localhost identified by "abc";
# 如果你不想test2有密码，可以再打一个命令将密码消掉。
mysql> grant select,insert,update,delete on mydb.* to test2@localhost identified by "";
# 9.查看字符集
mysql> show variables like 'character%';
```

### 数据库操作

```shell
# 创建数据库
create database 数据库名 charset=utf8;
# 删除数据库
drop database 数据库名;
# 切换数据库
use 数据库名;
# 查看当前选择的数据库
select database();
# 列出数据库
mysql> show databases;
```

### 数据表操作

```shell
# 查看当前数据库中所有表
show tables;
# 创建表 auto_increment表示自动增长
create table 表名(列及类型);
# 如：
create table students(
id int auto_increment primary key,
sname varchar(10) not null
);
# 修改表
alter table 表名 add|change|drop 列名 类型;
# 如：
alter table students add birthday datetime;
# 删除表
drop table 表名;
# 查看表结构
desc 表名;
# 更改表名称
rename table 原表名 to 新表名;
# 查看表的创建语句
show create table '表名';
```

### 修改表结构

```shell
# 1.更改表得的定义把某个栏位设为主键。
ALTER TABLE tab_name ADD PRIMARY KEY (col_name)
# 2.把主键的定义删除
ALTER TABLE tab_name DROP PRIMARY KEY (col_name)
# 3.在tab_name表中增加一个名为col_name的字段且类型为varchar(20)
alter table tab_name add col_name varchar(20);
# 4.在tab_name中将col_name字段删除
alter table tab_name drop col_name;
# 5.修改字段属性，注若加上not null则要求原字段下没有数据
alter table tab_name modify col_name varchar(40) not null;
# SQL Server200下的写法是：
Alter Table table_name Alter Column col_name varchar(30) not null;
# 6.如何修改表名：
alter table tab_name rename to new_tab_name;
# 7.如何修改字段名：
alter table tab_name change old_col new_col varchar(40);
# 必须为当前字段指定数据类型等属性，否则不能修改
# 8.用一个已存在的表来建新表，但不包含旧表的数据
create table new_tab_name like old_tab_name;
```

### 数据操作

```shell
# 查询
select * from 表名
# 增加
全列插入：insert into 表名 values(…)
缺省插入：insert into 表名(列1,…) values(值1,…)
同时插入多条数据：insert into 表名 values(…),(…)…;
或insert into 表名(列1,…) values(值1,…),(值1,…)…;
# 主键列是自动增长，但是在全列插入时需要占位，通常使用0，插入成功后以实际数据为准
# 修改
update 表名 set 列1=值1,… where 条件
# 删除
delete from 表名 where 条件
# 逻辑删除，本质就是修改操作update
alter table students add isdelete bit default 0;
# 如果需要删除则
update students isdelete=1 where …;
```

### 数据的备份与恢复

```shell
# 导入外部数据文本:
1.执行外部的sql脚本
当前数据库上执行:mysql < input.sql
指定数据库上执行:mysql [表名] < input.sql
2.数据传入命令 load data local infile “[文件名]” into table [表名];
备份数据库：(dos下)
mysqldump --opt school>school.bbb
mysqldump -u [user] -p [password] databasename> filename (备份)
mysql -u [user] -p [password] databasename < filename (恢复)
```

### 卸载mysql

```shell
dpkg --list|grep mysql        #在终端中查看MySQL的依赖项
sudo apt-get remove mysql-common  #卸载
sudo apt-get autoremove --purge mysql-server-8.0
##sudo apt-get autoremove --purge mysqlxxx

# 清理残留数据
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
# 再次查看MySQL的剩余依赖项：
dpkg --list|grep mysql
# 继续删除剩余依赖项，如：sudo apt-get autoremove --purge mysql-apt-config
#删除原先配置文件
sudo rm -rf /etc/mysql/ /var/lib/mysql
sudo apt autoremove
sudo apt autoreclean(如果提示指令有误，就把reclean改成clean)
```

## 部署PHP

```shell
# 安装
sudo apt-get install php
# 查看
sudo php -v

# 关联apache2
sudo apt-get install libapache2-mod-php
# 关联MySQL
sudo apt-get install php-mysql
```

## 测试LAMP环境

```shell
# 进入前端页面部署目录
cd /var/www/html
# 创建两个文件
vi test.php
<?php
phpinfo();
?>
vi test.html
<h1>Test HTML</h1>
```

浏览器访问 `IP/test.php` 和 `IP/test.html` 出来对应页面则说明LAMP环境搭建成功.
