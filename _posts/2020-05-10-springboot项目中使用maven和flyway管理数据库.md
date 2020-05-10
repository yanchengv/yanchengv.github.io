---
layout: post
title:  "springboot项目中使用maven和flyway管理数据库"
date: '2020-05-10 16:24:26'
categories: java
---

### 一、前言

Flyway 是一款开源的数据库版本管理工具，它更倾向于规约优于配置的方式。Flyway 可以独立于应用实现管理并跟踪数据库变更，支持数据库版本自动升级，并且有一套默认的规约，不需要复杂的配置，Migrations 可以写成 SQL 脚本，也可以写在 Java 代码中，不仅支持 Command Line 和 Java API，还支持 Build 构建工具和 Spring Boot 等，同时在分布式环境下能够安全可靠地升级数据库，同时也支持失败恢复等。
Flyway Ruby on Rails 的 Active Record migrate类似，但是没有Rails的功能强大和方便。

### 二、flyway基础

flyway要求为每一次数据库schema变更创建一个sql语句，以V（双下划线）作为前缀（比如V1 create_table_A.sql, V2__create_index_on_field_1。）

配置好数据库连接，执行flyway migrate命令，flyway会自动执行需要的脚本（比如在空数据库执行，会顺序执行V1和V2两个脚本，而在和V1版本一致的数据库执行，只会执行V2脚本），把数据库更新到最新状态。

在第一次执行flyway migrate的时候，flyway会在数据库中建立一张默认命名为schema_version的表，用于跟踪该数据库实例的schema版本变更。

除了migrate，flyway还支持clean, validate, baseline等命令。具体可以查看flyway document.

### 三、flyway在springboot中的使用

#### 1、项目结构
    
  下面是在标准的maven文件夹结构中，增加了/src/main/db/migration，用于存放数据库脚本。
  

```
src
|--main   
   |--java
   |--resources
      |--db
         |--migration
            |--V1.0__create_admins.sql
      |--application.yml
|--test

pom.xml
```

#### 2、flyway的配置

* 首先pox.xml中添加依赖：

```
 <dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
    <!--如果不配置scope，spirng项目启动时，就会自动检测数据库版本，如果不是最新的，则自动更新。如果scope设置为test，那么只有在测试环境下自动检测执行-->
    <scope>test</scope>
</dependency>

```

* application.yml的配置：

```
spring:
  datasource:
    driver-class-name: org.postgresql.Driver
    url: jdbc:postgresql://localhost:5432/my_java
    username: postgres
    password: 1234


  flyway:
    enabled: true
    locations: classpath:/db/migration #迁移脚本的位置，默认db/migration
    baseline-on-migrate: true #当迁移时发现目标schema非空，而且带有没有元数据的表时，是否自动执行基准迁移，默认false
    baseline-version: 1 #开始执行基准迁移时对现有的schema的版本打标签，默认值为1
    validate-on-migrate: true #迁移时是否校验，默认为true
    table: schema_migrations #版本表名称，默认是 flyway_schema_history

```

***如果不添加```<scope>test</scope>``` 。这样配置好后， 使用mvn spring-boot:run启动项目就会自动执行sql脚本创建表。如果scope设置为test，那么正常启动项目不会再自动执行sql脚本，只有在测试环境下自动检测执行。***
     

### 三、通过maven使用flyway  
  
***如果在项目启动时不想自动更新sql脚本更新数据库，那么就要使用命令行 ```mvn flyway:migrate``` 执行***

flyway支持命令行，java api，maven，gradle等各种方式调用。我的项目使用maven进行管理，所以选择了通过maven使用flyway的方式。

***如果想在控制台使用 ```mvn flyway:migrate```、```mvn flyway:info``` 等命令需要进行如下配置***

#### 1、pom.xml的配置

```
 <!-- 通过mvn flyway:migrate命令自动migrate数据库。 -->
            <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>${flyway.version}</version>
                <dependencies>
                    <!-- 配置flyway使用的数据库驱动 -->
                    <dependency>
                        <groupId>org.postgresql</groupId>
                        <artifactId>postgresql</artifactId>
                        <version>${postgresql.version}</version>
                    </dependency>
                </dependencies>
                <configuration>
                    <!-- 配置目标数据库连接 -->
                    <user>postgres</user>
                    <password>1234</password>
                    <driver>org.postgresql.Driver</driver>
                    <url>jdbc:postgresql://localhost:5432/my_java</url>
                    <!-- 配置数据库脚本位置，本里中对应admin/src/main/resources/db/migration -->
                    <locations>
                        <location>admin/src/main/resources/db/migration</location>
                    </locations>
                    <!--版本表名称-->
                    <schemas>
                        <schema>schema_migrations</schema>
                    </schemas>
                    <!-- 设置sql脚本文件的编码 -->
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>

```      

* 配置后就能在使用 `mvn flyway:migrate` 等命令了。


### 四、使用flyway同步数据库举例

假设使用场景如下：

* Day 1：项目启动，开发A和B把数据库schema大致定，开始工作。
 
A或B需要在db/migration目录建立数据库schema脚本，命名为V1__initialize.sql，提交到到github。A和B获取最新代码，并执行migrate命令，初始化本地数据库（并创建flyway版本控制表）。

```
mvn flyway:migrate

```

* Day 2：A在开发中需要修改table1，B在开发中需要新建table2，对应的修改应该先反映到migration下：

```
db/migration
   |--V1__initialize.sql        //创建于Day 1
   |--V2__update_table_1.sql    //A创建于Day 2
   |--V3__add_table_2.sql       //B创建于Day 2
   
```

然后A和B获取最新代码，重新执行flyway migrate，得到对方的更新。

*以上场景，A和B有可能都把文件前缀命名为V2（因为不知道对方需要更新），这里需要A和B协调后重命名其中一个为V3。*


### 五、flyway常用配置说明

* flyway.baseline-description对执行迁移时基准版本的描述.

* flyway.baseline-on-migrate当迁移时发现目标schema非空，而且带有没有元数据的表时，是否自动执行基准迁移，默认false.

* flyway.baseline-version开始执行基准迁移时对现有的schema的版本打标签，默认值为1.

* flyway.check-location检查迁移脚本的位置是否存在，默认false.

* flyway.clean-on-validation-error当发现校验错误时是否自动调用clean，默认false.

* flyway.enabled是否开启flywary，默认true.

* flyway.encoding设置迁移时的编码，默认UTF-8.

* flyway.ignore-failed-future-migration当读取元数据表时是否忽略错误的迁移，默认false.

* flyway.init-sqls当初始化好连接时要执行的SQL.

* flyway.locations迁移脚本的位置，默认db/migration.

* flyway.out-of-order是否允许无序的迁移，默认false.

* flyway.password目标数据库的密码.

* flyway.placeholder-prefix设置每个placeholder的前缀，默认${.

* flyway.placeholder-replacementplaceholders是否要被替换，默认true.

* flyway.placeholder-suffix设置每个placeholder的后缀，默认}.

* flyway.placeholders.[placeholder name]设置placeholder的value

* flyway.schemas设定需要flywary迁移的schema，大小写敏感，默认为连接默认的schema.

* flyway.sql-migration-prefix迁移文件的前缀，默认为V.

* flyway.sql-migration-separator迁移脚本的文件名分隔符，默认__

* flyway.sql-migration-suffix迁移脚本的后缀，默认为.sql

* flyway.tableflyway使用的元数据表名，默认为schema_version

* flyway.target迁移时使用的目标版本，默认为latest version

* flyway.url迁移时使用的JDBC URL，如果没有指定的话，将使用配置的主数据源

* flyway.user迁移数据库的用户名

* flyway.validate-on-migrate迁移时是否校验，默认为true.

### 参考

[官网文档](https://flywaydb.org/documentation/)

[使用maven和flyway管理数据库schema](https://woodylic.github.io/2017/03/23/manage-database-migration-using-maven-and-flyway/)
