---
title: "Java 部署"
date: 2020-08-30T17:47:43+08:00
draft: false
toc: false
images:
tags: [开发,部署,socker,分布式]
---
## 应用开发流程
- 产品调研
- 产品设计
- 系统设计 -> 全后端文档等
- 开发
- 测试
- 部署
## Web应用是怎么工作的
- 默认443端口
-  每个HTTP请求背后都是一台主机
## 分布式
- 解决用户量大的问题
- - 垂直扩展 ：购入更好的机器
- - 水平扩展 ： 购入更多的机器（分布式）
> - 负载均衡器 Load Balance
> 1. 负载均衡
> 2. 容灾（单点故障）：一个服务器故障也不会影响到业务  
- 如何保持数据一致性 
- - 让负载均衡器保证同一个用户的请求都发到同一台机器上，但是HTTP是无状态的，同一个用户的不同请求是没有先后顺序的，没有用户标识，都是HTTP请求，即使有Cookie也不行
- - 单一数据源：数据都存在同一个数据库，即使服务器杀死重启复原数据都不会改变，但是不能够避免单点故障的问题，数据库会出问题
> 1. 大多数情况下，数据库都是稳定的。
> 2. 数据库会有备份
## 部署
- 将开发好的程序放在服务器上
- - 监听端口
- - 相应HTTP请求
- - 进行预定义的业务逻辑处理
- 产品不停迭代
1. 灰度发布：部分停止服务器进行更新。剩余的服务器有可能承受不了业务请求的数量而垮掉，即使停止的重新工作也不一定能够解决。
2. 半夜更新
- - 部署的版本需要不停进行更新
- - 分布式更新
- 环境问题
- - 开发环境（测试环境）
- - 预发布环境（预生产环境，可能是生产环境的副本）
- - 生产魂晶（正式环境）
- 环境的兼容性问题
- - 硬件、软件
>- 开发的软件、运行的软件、服务器系统
- - 数据库等
>- 数据库版本、内容、结构
## 发布和部署程序
- 如何在生产环境下运行
-  解决
- - 编写的代码
- - 依赖的第三方库
- - 依赖的特殊环境配置（数据库、缓存）
- - 稳定性
- - 升级和回滚

## 使用Maven exec plugin
- 自动将所有的传递性依赖加入
- - 优点：简单
- - 缺点： 不适用与自动化的场景
>- 运行的时候需要把代码都下载下来，才能够运行。不能够把代码编译成字节码，所以需要绑定到编译后的阶段才能够运行

- exec:exec -> 在一个独立的进程中执行一个程序
- exec:java -> 在同一个JVM里执行java
```java
<plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>3.0.0</version>
      <configuration>
      <-- executable :要运行什么程序 -->
          <executable>java</executable>
          <arguments>
           <argument>-classpath</argument>
            <!-- automatically creates the classpath using all project dependencies,
                 also adding the project build directory
                 自动给jvm运行的class生成classpath（全地址），避免填写的麻烦-->
            <classpath/>
           <-- argument运行程序是用的参数（jvm要启动的类名） -->
            <argument>com.example.Main</argument>
          </arguments>
        </configuration>
</plugin>
```
- 命令行：可执行程序+参数
- - mvn exec:exec 执行java
- - mvn exec:exex -x 打印出执行的信息

## jar包
- 编译后的代码打包
- - java 启动方式(两种方式都是一样的，因为jar包里面有清单文件（manifest），文件里面包含mainClass)
1. java -classpath mainClass
2. java -jar jarName.zip/jarName.jar
- jar包和war包区别
- - jar包本质是一个压缩包，把所以代码打包压缩，运行信息、依赖、内嵌Tomcat等等
- - war包也是压缩包，但是只包含依赖（资源），没有内嵌Tomcat，不能够运行。如果要运行就要放在servlet容器里面运行
- - Eg：SpringBoot.jar = war + Tomcat(Servlet容器提供底层HTTP互交)
- SpringBoot repackage: 创建一个自动可执行的jar或war文件。它可以替换常规的artifact(手动运行)，或者用一个单独的classifier附属在maven构建的生命周期中，使得程序可以自动执行。
- 优点：简单可靠
- 缺点：依赖于JVM环境，虽然碰到问题的概率低

## 部署
- 域名指向服务器IP地址
- - 把Spring xml配置里面的`datasource.url`更改成IP地址（网域名）
`spring.datasource.url = jdbc:mysql://localhost:3306/user`
- NGINX 监听80端口

#### maven 直接运行源代码
#### jar方式
#### Docker 方式







 