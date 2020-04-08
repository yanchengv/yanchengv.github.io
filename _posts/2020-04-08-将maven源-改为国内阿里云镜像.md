---
published: false
layout: post
date: '2020-04-08 20:15:26'
categories: java
---
## 将maven源改为国内阿里云镜像

大家都知道因某种原因，maven的库在中国大陆非常慢,因此需要切换成国内的源。

1.[进入阿里源官网地址](https://maven.aliyun.com/mvn/view)。找到public仓库地址

2.修改maven根目录下的conf文件夹中的setting.xml文件，内容如下：

```
<mirrors>
    <mirror>
    <id>aliyunmaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
    <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
</mirrors>

```

换为国内镜像，让你感受飞一般的感觉~