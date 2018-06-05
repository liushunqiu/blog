---
title: Linux各种命令
date: 2018-04-29 22:59:14
tags: Linux
---
1.按进程消耗内存多少排序的方法，查看占据内存最大的前10个进程：
```
ps -e -o 'pid,comm,args,pcpu,rsz,vsz,stime,user,uid' --sort -rsz|head -11
```
2.vmstat 1 查看CPU消耗
3.kill掉对应关键字的线程
```
ps -ef|grep 查询关键字 |grep -v grep|cut  -c 9-15|xargs kill -9   
```
4.通过pid查看占用端口
```
netstat -nap|grep 进程PID
```
5.通过端口查看占用进程
```
netstat -nap|grep 端口号
备注：windows下查看端口占用
如:查询占用了8080端口的进程：netstat -ano|findstr "8080"
```
6.mvn 编译过慢
```
mvn clean package -T 1C -Dmaven.test.skip=true  -Dmaven.compile.fork=true  
```
