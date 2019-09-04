---
title: postgresql导入带ID数据后,rails项目新创建时主键重复问题
layout: post
date: '2019-09-04 15:30:00'
categories: rails
---

**如果项目中部分数据是从其他数据库导过来的，而且导入的数据自带主键ID。这样会导致新创建新数据时主键重复。为了避免主键重复，需要重新设置表的 id sequence 值变成最大，就不会有主键重复的问题了**

```
sequence = "tablename_id_seq"
ActiveRecord::Base.connection.execute("SELECT setval('#{sequence}', 100, true)")
```
例如：
导入了100个带有主键的用户数据到users表中，此时用户表最大id是100。当通过rails新建数据时，主键依旧会从1开始，这样就会创建失败提示主键不能重复。解决办法是把users表ID设置为101开始递增

```
sequence = "users_id_seq"
ActiveRecord::Base.connection.execute("SELECT setval('#{sequence}', 100, true)")
```
这样设置后，通过rails项目创建用户时，users的主键会从101开始递增。

备注：
执行此方法时
`ActiveRecord::Base.connection.execute("SELECT setval('users_id_seq', 100, true)")`

变为的sql语句为：

`SELECT setval('users_id_seq', 100, true)`

参考: 
[ruby-china](https://ruby-china.org/topics/33256)