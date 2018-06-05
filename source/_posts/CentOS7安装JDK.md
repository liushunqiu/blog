---
title: CentOS7安装JDK
date: 2018-04-29 18:35:52
tags:
	- Linux
---
1.更新系统软件 yum update
2.cd /usr/local/src
3.从Oracle官网下载 JDK
4.解压 tar -zxf jdk.tar.gz
5.添加到环境变量，内容如下:
```java
#jdk
export JAVA_HOME=/usr/local/src/jdk1.8.0_144
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```
然后执行 source /etc/profile
6.验证 java -version
