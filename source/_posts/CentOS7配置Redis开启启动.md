---
title: CentOS7配置Redis开启启动
date: 2018-04-29 18:28:31
tags: 
	- Shell
	- Linux
	- Redis
---

1.cd /usr/local/src
2.wget http://download.redis.io/releases/redis-4.0.2.tar.gz
3.tar xzf redis-4.0.2.tar.gz
4.cd redis-4.0.2
5.make
6.vim redis.conf,内容如下:
```java
#bind 127.0.0.1
daemonize yes
```
7.vim /etc/init.d/redis,内容如下:
```java
#!/bin/sh
#Simple Redis init.d script conceived to work on Linux systems
#as it does use of the /proc filesystem.
#chkconfig: 2345 80 90
#description:auto_run
REDISPORT=6379
EXEC=/usr/local/src/redis-4.0.1/src/redis-server
CLIEXEC=/usr/local/src/redis-4.0.1/src/redis-cli

PIDFILE=/var/run/redis_6379.pid
CONF="/usr/local/src/redis-4.0.1/redis.conf"

case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
    *)
        echo "Please use start or stop as first argument"
        ;;
esac
```
8.修改文件执行权限 chmod +x /etc/init.d/redis
9.设置开机启动 
# 尝试启动或停止 redis
service redis start
service redis stop
# 开启服务自启动
chkconfig redis on