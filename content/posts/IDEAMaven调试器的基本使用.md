---
title: "IDEAMaven调试器的基本使用"
date: 2020-05-06T17:06:17+08:00
draft: false
---


#### SpotBugs
- Maven 生命周期  
设置生命周期后的Maven会在相应的阶段执行
Picture 设置
- 生命周期对应执行的工作需要绑定插件进行设置
1. complie —— goal ——> maven-compilen-plugin
2. test —— goal ——> surefire 测试插件
3. verlfy —— goal ——> checkstyle（非核心插件可以绑定到任何阶段

## Maven 生命周期
- 编译源代码
- - mvn compile
- 发布项目
- - mvn deploy
- 编译测试源代码
- - mvn test-compile
- 运行应用程序中的单元测试
- - mvn test
- 生成项目相关信息的网站
- - mvn site
- 编译源代码
- - mvn compile
- 清除项目目录中的生成结果
- - mvn clean
根据项目生成的jar
mvn package
在本地Repository中安装jar
mvn install
生成eclipse项目文件
mvn eclipse:eclipse
启动jetty服务
mvn jetty:run
启动tomcat服务
mvn tomcat:run
清除以前的包后重新打包，跳过测试类
mvn clean package -Dmaven.test.skip=true

[https://segmentfault.com/a/1190000015077021]