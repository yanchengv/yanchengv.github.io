---
published: false
layout: post
date: '2020-03-22 17:35:26'
categories: java
---
## Mac配置电脑JAVA环境

1.查看java版本 ,没有下载
在终端输入下面命令来查看是否有java环境有则显示版本，没有弹出提示框，点详情按钮下载安转即可
`java -version`
下载安装成功后再次输入，结果如下：

```
java version "11.0.6" 2020-01-14 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.6+8-LTS)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.6+8-LTS, mixed mode)
```
2.查看JAVA_HOME
 控制台数据 `echo $JAVA_HOME` 显示结果如下：
 
 `/Library/Java/JavaVirtualMachines/jdk-11.0.6.jdk/Contents/Home`
 
 能看到jdk的安装目录说明JAVA_HOME环境已按照。如果不显示则继续下面步骤进行安装。

3.使用命令`/usr/libexec/java_home`来定位JAVA_HOME的安装目录。可以看到输出：`/Library/Java/JavaVirtualMachines/jdk-11.0.6.jdk/Contents/Home`这就是我电脑上，java home的路径。

4.在 `~/.bash_profile`文件里面添加下面代码

```
 export JAVA_HOME=$(/usr/libexec/java_home)
 export PATH=$JAVA_HOME/bin:$PATH
 export CLASS_PATH=$JAVA_HOME/lib
```

让配置立即生效 `source ~/.bash_profile`
查看已插入的JAVA_HOME ,输入域`echo $JAVA_HOME`如果可以看到刚刚配置的PATH则说明配置成功！




 
 
 
 


