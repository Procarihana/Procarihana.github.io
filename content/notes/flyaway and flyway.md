---
title: "Flyaway and Flyway"
date: 2020-09-06T11:42:45+08:00
draft: true
tags: ["flyway","flyaway"]
---
#### Docker mysal 迁移h2 的数据库
- 建立一个储存数据库的目录（datadir）  mkdir
```
    mkdir mysql-data
``` 
- 在docker 上面开设mysql的数据库  
```
    docker run --name mockNews-mysql -v `pwd`/mysql-mockNewsData:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=hana -e MYSQL_DATABSE=databasename -p 3306:3306 -d mysql
```
- - `pwd`只要MAC能够，Windows要用全路径名
- 更改有flyway的连接（用户名为root，密码为docker run 上面设置的密码）和Mybatis的config.xml里面的url，
- - config


    <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/news?characterEncoding=utf-8"/>
                <property name="username" value="root"/>
                <property name="password" value="hana"/>
            </dataSource>
### flyway 数据库自动化管理   

 **pom.xml**
```
     <build>
        <plugins>

            <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>5.2.4</version>
                <configuration>
                    <url>jdbc:mysql://localhost:3306/NEWS</url>
                    <user>root</user>
                    <password>hana</password>
                </configuration>
```
#### or
```
    <configuration>
                <url>jdbc:mysql://localhost:3306/news</url>
                <user>root</user>
                <password>hana</password>
     <property name="url"value="jdbc:mysql://localhost:3306/news?characterEncoding=utf-8"/>
```

  **在`src/main/resources/db/migration`添加数据库需要重置的内容**
#### or --> 不需要启动项目就能够运行
- 在Spring配置里面配置好Spring.datasource.url、username、password、driver-class-name 
- 使用flyway-core maven 
1. 添加 等等储存数据库需要的变更还原……
```
V1__Initial_version.sql
```
```
V2__Add a new table.sql
```
- 添加mysql connect java 的maven  
```
url=jdbc:mysql://localhost/databasename   
user = root   
password = docker设置的登录mysql  
```
（docker的mysql要在运行的状态，没有的话就要去docker -d（当前）或者-it（后台））

```
    docker run mysqlname -it mysql
```
- 运行 
```
mvn flyway:migrate
```

#### 通过索引，让数据更新

**问题：**
1. flyway tablename 
- SQL分大小写，有小写tablename在创建后出现不存在的反馈
-  Found non-empty schema(s) `user` without schema history table! Use baseline() or set baselineOnMigrate to true to initialize the schema history table.

#### flyaway
- 数据库在使用的时候会不断变更，有不同的版本，flyaway可以储存数据库不同的版本，从而可以变更不同版本的**结构**
- flyaway需要在插件的地方修改URL，添加Username和password
- 即使数据库删掉也可以快速回复结构
- flyaway 数据库结构管理的自动化


         <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>5.2.4</version>
                <configuration>
                    <url></url>
                    <user></user>
                    <password></password>
                </configuration>

            </plugin>


- - baseline

>- 对已经存在数据库Schema结构的数据库一种解决方案。实现在非空数据库新建MetaData表，并把Migrations应用到该数据库；也可以在已有表结构的数据库中实现添加Metadata表。

- - clean

>- 清除掉对应数据库Schema中所有的对象，包括表结构，视图，存储过程等，clean操作在dev 和 test阶段很好用，但在生产环境务必禁用。

- - info

>- 用于打印所有的Migrations的详细和状态信息，也是通过MetaData和Migrations完成的，可以快速定位当前的数据库版本。

-  -repair

>- repair操作能够修复metaData表，该操作在metadata出现错误时很有用。

- - undo

>- 撤销操作，社区版不支持。

- - validate

>- 验证已经apply的Migrations是否有变更，默认开启的，原理是对比MetaData表与本地Migrations的checkNum值，如果值相同则验证通过，否则失败。

#### PS
- flyway执行migrate必须在空白的数据库上进行，否则报错；
- 对于已经有数据的数据库，必须先baseline，然后才能migrate；
- clean操作是删除数据库的所有内容，包括baseline之前的内容；
- 尽量不要修改已经执行过的SQL，即便是R开头的可反复执行的SQL，它们会不利于数据迁移；
