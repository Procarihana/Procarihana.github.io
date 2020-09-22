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
- 自动运行的部分记得不要加生命周期！！！不然就会卡在那里的！
- configuration 要放在 execution 外面，否则会识别不到executable 
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
>- mvn compile exec:exec
- - mvn exec:exex -x 打印出执行的信息


## 附
- 启动jar包
- 自动启动docker，启动数据库Mysql,测试完毕后自动销毁
```java
 <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>3.0.0</version>
                <executions>
                    <execution>
                        <id>start java</id>
                        <!--executable :要运行什么程序 -->
                        <goals>
                            <goal>exec</goal>
                        </goals>
                        <configuration>
                            <executable>java</executable>
                            <arguments>
                                <argument>-classpath</argument>
                                <!-- automatically creates the classpath using all project dependencies,
                                     also adding the project build directory
                                     自动给jvm运行的class生成classpath（全地址），避免填写的麻烦-->
                                <classpath/>
                                <!-- argument运行程序是用的参数（jvm要启动的类名） -->
                                <argument>com.Application</argument>
                            </arguments>
                        </configuration>
                    </execution>
                    <execution>
                        <!-- id用于区分-->
                        <id>start-test-database</id>
                        <!-- 把这个maven 绑定 在某个 生命周期里运行-->
                        <phase>pre-integration-test</phase>
                        <goals>
                            <!-- goal为java表示在java里运行，而为exec则是在外部运行-->
                            <goal>exec</goal>
                        </goals>
                        <configuration>
                            <longModulepath>false</longModulepath>
                            <!--executable 用于运行的程序-->
                            <executable>docker</executable>
                            <!-- argument用于docker运行测试数据库 -->
                            <arguments>
                                <argument>run</argument>
                                <argument>--name</argument>
                                <argument>test-mysql</argument>
                                <argument>-e</argument>
                                <argument>MYSQL_ROOT_PASSWORD=hana</argument>
                                <argument>-e</argument>
                                <argument>MYSQL_DATABASE=test_user</argument>
                                <argument>-p</argument>
                                <argument>3307:3306</argument>
                                <argument>-d</argument>
                                <argument>mysql</argument>
                            </arguments>
                        </configuration>
                    </execution>
                    <execution>
                        <!--因为docker运行后，数据库的启动还是需要一点时间的，并不能够马上运行起来，
                         所以需要等待。而且execution的执行顺序是按照声明的顺序执行的-->
                        <id>wait-test-database</id>
                        <phase>pre-integration-test</phase>
                        <goals>
                            <goal>exec</goal>
                        </goals>
                        <configuration>
                            <longModulepath>false</longModulepath>
                            <executable>sleep</executable>
                            <arguments>
                                <argument>30</argument>
                            </arguments>

                        </configuration>
                    </execution>
                    <execution>
                        <!-- 用于销毁测试数据库-->
                        <id>teardown-test-database</id>
                        <phase>post-integration-test</phase>
                        <goals>
                            <goal>exec</goal>
                        </goals>
                        <configuration>
                            <longModulepath>false</longModulepath>
                            <executable>docker</executable>
                            <arguments>
                                <argument>rm</argument>
                                <argument>-f</argument>
                                <argument>test-mysql</argument>
                            </arguments>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

```
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
####  NGINX 监听80端口，负载均衡器
- - 流量均分到三个服务器上
- - NGINX如果出现问题能够在两秒内迅速启动
- - docker 启动NGINX
>- docker run --name springboot-nginx restart=always -v `pwd`/nginx/nginx.conf:/etc/nginx/nginx.conf:ro -d -p 8085:80 nginx
>- --restart=always 碰到问题时自动重启
#### redis 进行缓存
- - 通过docker 启动redis
####  jenkins
- - 通过docker 启动 jenkins
`docker run -p 8080:8080 -v `pwd`/jenkins:/var/jenkins_home jenkins/jenkins`
``
#### maven 直接运行源代码
- mvn compile exec:exec
#### jar方式
- [×] java -jar jarname -Dserver.port=8081 
- java -Dserver.port=8081 -jar jarName
>- java -Dserver.port=8081 -jar target/spring-boot-0.0.1-SNAPSHOT.jar
- - java -jar 后面程序运行的内容会被传递到jar 包运行的main里面，所以传递port是数据应用成语的参数，-jar 属于java 程序的参数
#### Docker 方式
- 建立dockerfile
```
FROM openjdk:8u265-slim-buster

RUN mkdir /dockerworkdir

WORKDIR /dockerworkdir

COPY  target/spring-boot-0.0.1-SNAPSHOT.jar /dockerworkdir

EXPOSE 8080

CMD ["java","-jar","spring-boot-0.0.1-SNAPSHOT.jar"]
```
- `docker build .`
- - 如何修改已经bulid 的镜像
- - 可以把需要更改的配置文件映射给docker 容器
- docker run

## NGINX 分布式部署
- 本地nginx.conf 配置分布式
- 流量的配置取决于nginx，默认的是轮流分配
- nginx 能够自动检测没有启动到的服务器，并停止给这个没有启动的服务器分配流量
- - nginx 监听 80 端口， 所有流量都转发`proxy_pass`
- - 但是docker 和 宿主机是完全隔离的，所以流量直接在docker 里面转是无效的。所以nginx 需要通过局域网来访问（服务器IP地址）
- - ifconfig 可以获得本机局域网IP地址 -> 本机：192.168.1.7 
>- server  192.168.1.7:port;
- 更改过nginx 后需要重启生效
```
events{}
http {
    upstream backend {
        server backend1.example.com;
        server backend2.example.com;
        server 192.0.0.1 backup;
    }
    
    server {
      listen 80;
        location / {
            proxy_pass http://backend;
        }
    }
}
```
https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/
- docker 启动 nginx 
- - 增加`--restart=always`防止 NGINX 挂掉（重启速度很快，所以不用担心）







 