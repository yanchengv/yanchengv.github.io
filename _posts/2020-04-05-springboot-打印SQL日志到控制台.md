---
published: true
layout: post
date: '2020-04-05 12:55:26'
categories: java
---
## Springboot配置application.yml文件，打印SQL日志到控制台

```
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/my_java
    username: postgres
    password: 123
    driver-class-name: org.postgresql.Driver


mybatis:
  configuration:
    map-underscore-to-camel-case: true #驼峰命名规范 如：数据库字段是order_id 那么实体字段就要写成 orderId
    logImpl: org.apache.ibatis.logging.stdout.StdOutImpl #sql语句输出到控制台
    
```
