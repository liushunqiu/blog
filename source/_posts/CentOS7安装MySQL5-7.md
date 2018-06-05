---
title: CentOS7安装MySQL5.7
date: 2018-04-29 22:25:20
tags:
	- Linux
	- MySQL
---
1.添加 mysql yum Repository
```java
wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
yum localinstall mysql57-community-release-el7-11.noarch.rpm
```
2. 安装mysql-server:
```java
yum install mysql-community-server
```
3. mysql服务 
```java
a).启动mysql服务:service mysqld start 
b).查看mysql服务状态:service mysqld status
c).重启mysql服务: service mysqld restart
```
4.修改初始密码
a).初始密码在 /var/log/mysqld.log 文件中
```java
grep 'temporary password' /var/log/mysqld.log
```
5.修改 root 密码
```java
a ). mysql -u root -p 
b ). ALTER USER 'root'@'localhost' IDENTIFIED BY '1qaz@WSX3edc';  
c ). FLUSH PRIVILEGES;
d). exit;
```
6.重启mysql
```java
service mysqld restart 
```
7.设置 mysql 远程访问
```java
a). vim /etc/my.cnf 增加内容：bind-address = 0.0.0.0
b). 增加慢查询
slow-query-log = 1
slow-query-log-file=/var/lib/mysql/mysql_slow.log
long_query_time=1
```
8.登录 mysql -u root -p 
```java
mysql> use mysql;
mysql> select user,host from user;
将root用户host更新成 %:
mysql>  update user set host = '%' where user = 'root';
```
9.重启mysql 
10.查看慢查询参数 
```java
mysql> show variables like '%query%';
```