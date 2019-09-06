# mariadb centos7 安装指南

> 非原创，在[青青子衿zz ](<https://www.cnblogs.com/zhanzhan/p/7729981.html>)、[浮尘的](https://www.cnblogs.com/rxbook/p/8110143.html)文章基础上整理的。

目录
===

-  [安装MariaDB](#安装MariaDB)
- [配置MariaDB的字符集](#配置MariaDB的字符集)
- [开放防火墙端口](#开放防火墙端口)
- [开启远程连接权限](#服务管理)
	- [直接改root账号host（方法1）](#直接改root账号host（方法1）)
	- [创建可登陆用户并给与特定权限（方法2）](#创建可登陆用户并给与特定权限（方法2）)

## 1、安装MariaDB

安装命令
```bash
yum -y install mariadb mariadb-server
```

安装完成MariaDB，首先启动MariaDB

```bash
systemctl start mariadb
```

设置开机启动

```bash
systemctl enable mariadb
```

接下来进行MariaDB的相关简单配置

```bash
mysql_secure_installation
```

首先是设置密码，会提示先输入密码

```bash
Enter current password for root (enter for none):<–初次运行直接回车
```

设置密码

```bash
Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码
```

其他配置

```bash
Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车

Disallow root login remotely? [Y/n] <–是否禁止root远程登录,回车,

Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车

Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车
```

初始化MariaDB完成，接下来测试登录

```bash
mysql -u root -p
```

完成。

## 2、配置MariaDB的字符集


文件/etc/my.cnf

```bash
vi /etc/my.cnf
```

在[mysqld]标签下添加

```bash
init_connect='SET collation_connection = utf8_unicode_ci' 
init_connect='SET NAMES utf8' 
character-set-server=utf8 
collation-server=utf8_unicode_ci 
skip-character-set-client-handshake
```

文件/etc/my.cnf.d/client.cnf

```bash
vi /etc/my.cnf.d/client.cnf
```

在[client]中添加

```bash
default-character-set=utf8
```

文件/etc/my.cnf.d/mysql-clients.cnf

```bash
vi /etc/my.cnf.d/mysql-clients.cnf
```

在[mysql]中添加

```bash
default-character-set=utf8
```

 全部配置完成，重启mariadb

```bash
systemctl restart mariadb
```

之后进入MariaDB查看字符集

``` bash
[root@localhost ~]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor. Commands end with ; or \g.
Your MariaDB connection id is 14
Server version: 5.5.56-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show variables like "%character%";show variables like "%collation%";
+--------------------------+----------------------------+
| Variable_name | Value |
+--------------------------+----------------------------+
| character_set_client | utf8 |
| character_set_connection | utf8 |
| character_set_database | latin1 |
| character_set_filesystem | binary |
| character_set_results | utf8 |
| character_set_server | latin1 |
| character_set_system | utf8 |
| character_sets_dir | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
8 rows in set (0.01 sec)

+----------------------+-------------------+
| Variable_name | Value |
+----------------------+-------------------+
| collation_connection | utf8_general_ci |
| collation_database | latin1_swedish_ci |
| collation_server | latin1_swedish_ci |
+----------------------+-------------------+
3 rows in set (0.00 sec)
```

##  3、开放防火墙端口

查看防火墙是否开放端口，提示FirewallD is not running可能没有开启防火墙

```bash
firewall-cmd --permanent --zone=public --add-port=3306/tcp
```

firewalld状态，发现当前是dead状态，即防火墙未开启。

```bash
systemctl status firewalld
```

通过systemctl start firewalld开启防火墙，没有任何提示即开启成功。

```bash
systemctl start firewalld
```

再次通过systemctl status firewalld查看firewalld状态，显示running即已开启了。

```bash
systemctl status firewalld
```

如果要关闭防火墙设置，可能通过systemctl stop firewalld这条指令来关闭该功能。

```basdh
systemctl stop firewalld
```

再次执行执行firewall-cmd --permanent --zone=public --add-port=3306/tcp，提示success，表示设置成功，这样就可以继续后面的设置了。

```bash
firewall-cmd --permanent --zone=public --add-port=3306/tcp
```

## 4、开启远程连接权限
登陆mysql数据库

```bash
mysql -u root -p
```

查看user表

```mysql
mysql> use mysql;
Database changed
MariaDB [mysql]> select host,user,password from user;
+-----------+------+-------------------------------------------+
| host      | user | password                                  |
+-----------+------+-------------------------------------------+
| localhost | root | *273FF523C1DDE30E1292B18A9F6119695F5436B6 |
| 127.0.0.1 | root | *273FF523C1DDE30E1292B18A9F6119695F5436B6 |
| ::1       | root | *273FF523C1DDE30E1292B18A9F6119695F5436B6 |
+-----------+------+-------------------------------------------+
```

在数据库`mysql` 中的`user`表中可以看到默认是只能本地连接的，所有可以添加一个用户

#### 直接改root账号host（方法1）

```mysql
update user set host = 'localhost' where user = 'root';
// 此提示不用管
ERROR 1062 (23000): Duplicate entry 'localhost-root' for key 'PRIMARY'
// 刷新权限
flush privileges;

```



#### 创建可登陆用户并给与特定权限（方法2）

语法

```mysql
CREATE USER 'username'@'host' IDENTIFIED BY 'password';
```

如上sql语句创建一个登陆账号，建议指定特定ip或发开发是开放%

```mysql
# 针对特定ip
create user 'root'@'192.168.10.10' identified by 'password';

# 开放全部
 create user 'root'@'%' identified by 'password';
```

授权

```mysql
 # 给用户最大权限
 grant all privileges on *.* to 'root'@'%' identified by 'password';
 
 # 给部分权限(test 数据库)
 grant all privileges on test.* to 'root'@'%' identified by 'password' with grant option;
 
 # 刷新权限表
 
 flush privileges;
```

成功后即可远程连接





