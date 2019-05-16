---
title: linux 周期归档rails log logrotate日志分割
layout: post
date: '2019-05-16 19:57:26'
categories: ubuntu
---

**logrotate是一个让日志文件自动按周期归档的工具。以避免日志文件过大。**

**1. 安装**

Ubuntu： `sudo apt-get install logrotate`

redhat:  `sudo yum install logrotate`

**2. 配置**

```
$ sudo vim /etc/logrotate.conf
/path_to_app/log/production.log {
daily                       #按日阶段
missingok
rotate 7               #日志文件保留7天
compress           #压缩
delaycompress      #不压缩前一个(previous)截断的文件（需要与compress一起用）
dateext         #增加日期作为后缀，不然会是一串无意义的数字
copytruncate     #清空原有文件，而不是创建一个新文件
}
```

例如：

```
/dfs/deploy/mimas_deploy/current/log/*.log {
     daily
     missingok
     rotate 10
     compress
     delaycompress
     dateext
     copytruncate
 }
```


```
/dfs/deploy/adminmimas_deploy/current/log/*.log {
     daily
     missingok
     rotate 7
     compress
     delaycompress
     dateext
     copytruncate
 }
```

**3. 让配置生效**

`sudo /usr/sbin/logrotate /etc/logrotate.conf`

按上述配置完以后，日志文件会按配置的日期定期分割保存！