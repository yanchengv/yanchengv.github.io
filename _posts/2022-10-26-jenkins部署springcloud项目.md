---
title: jenkins部署springcloud项目
layout: post
date: '2022-10-26 15:10:40'
categories: java
---

1、源码管理
![](/20221026_jekins01.png)

2、构建打包前的配置
![](/20221022_jenkins02.png)

3、构建打包后的配置
![](/20221022_jenkins03.png)

4、服务器启动/停止脚本
    
* 		启动脚本start.sh
		 
```
echo '-------Starting-------'
cd /data/www/java/
nohup ${JAVA_HOME}/bin/java -jar balawo-order.jar &
echo 'start success'
```

*停止服务的 stop.sh脚本
```
	#!/bin/bash
echo "Stop Procedure : balawo-order.jar"
pid=`ps -ef |grep java|grep balawo-order.jar|awk '{print $2}'`
echo 'old Procedure pid:'$pid
if [ -n "$pid" ]
then
kill -9 $pid
fi
```