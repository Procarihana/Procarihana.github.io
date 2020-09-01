---
title: "Bean"
date: 2020-09-01T10:04:08+08:00
draft: false
toc: false
images:
tags: [Bean]
---
## Bean生成
- 一个对象生成Bean只能用一个方法配置，不能每个方法都对同一个对象生成bean
- class从java下一个文件夹开始~

  `class=java.com.service.OrderService`-->`class=com.service.OrderService`
- Bean 配置
1. xml（旧的）
- 新建一个```src/main/resources/applicationContext.xml```
- 复制

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">

	<!-- <context:component-scan base-package="com.in28minutes.spring.basics"/> -->

	<bean id="xmlStringBean1" class="java.lang.String">
		<constructor-arg value="stringBean1" />
	</bean>

	<bean id="xmlStringBean2" class="java.lang.String">
		<constructor-arg value="stringBean2" />
	</bean>

</beans>
```
- 修改bean id 和所在的class,不需要参数可以删掉
- 添加一个配置类(Eg:`XmlConfiguration.class`)，Bean会被加载。最好是放在一个包里面方便管理
```java
@Configuration
@ImportResource({"classpath*:applicationContext.xml"})
public class XmlConfiguration {
}
```

2. `@ammunition` 
- 新建一个配置类(Eg:`JavaConfiguration.class`)
```java
package com.in28minutes.springboot.tutorial.basics.example.application.context.java;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class JavaConfiguration {
	
	@Bean
	public String someDummyBean1() {
		return "someDummyBean1";
	}
	
	@Bean
	public String someDummyBean2() {
		return "someDummyBean2";
	}

}
```
- 修改`String someDummyBean1`为需要的（Eg：`UserService userService`）
- `return new UserService();`
3. 在Bean的类名前面声明`@Service`、`@Component`,就可以实现自动Bean声明，就不需要前面的两个方法.
```java
@Service
public class UserService{
    
}
```

**https://www.springboottutorial.com/spring-boot-java-xml-context-configuration**
