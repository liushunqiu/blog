---
title: CentOS7使用mailx发送邮件(通过postfix与mailx)
date: 2018-04-29 22:55:08
tags:
	- Linux
	- Shell
---
1.先卸载掉sendmail
```
yum remove -y sendmail
```
2.安装postdix
```
yum install -y postfix mailx cyrus-sasl-plain
```
3.这里我们使用163邮箱来发送邮件，首先到163邮箱官网里面设置smtp还有获取授权码
![](/images/mailxsend.png)
4.编辑vim /etc/mail.rc,编辑内容如下：
```
set from=（发送人）
set smtp=(发送邮件的stmp)smtps://smtp.163.com:994
set smtp-auth=login
set smtp-auth-user=(用户名)
set smtp-auth-password=(授权码)
set ssl-verify=ignore
set nss-config-dir=/etc/pki/nssdb
```
5.测试发送
```
echo “内容” | mailx -v -s " 标题" 收件人邮箱
```