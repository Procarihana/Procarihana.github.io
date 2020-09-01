---
title: "SpringBoot Blog"
date: 2020-09-01T08:01:27+08:00
draft: false
toc: false
images:
tags: [SpringBoot,Tomcat,Filter]
---
## Servlet

#### Tomcat 
- servlet容器
- catalina 

#### Filter 过滤器
- 是一个执行过滤任务的对象，
- doFilter 请求响应，也能够通过FilterChain抽根烟·传给下一个filter，实现过滤的功能

#### listerner
- Tomcat 容器启动是需要时间的，必须等待Tomcat容器启动之后，Servlet才能够启动
- 用于启动和关闭Spring容器的本体

#### Docker
- 用统一的环境处理软件交互，运行速度比虚拟机要快
- Docker一个进程就是一个虚拟机，监听一个端口，和宿主机没有任何的交互关系
- docker的端口和本机的端口没有关系，除非绑定

#### index.html
##### 渲染
- 后端渲染:根据用户请求的内容的不同，决定返回的内容
- 前端渲染：把后端返回的内容填充到页面上面
- 渲染模板：渲染的过程发生的地方是由在前端还是后端的进行渲染决定的


##### 浏览器
- 浏览器栏发出的请求都是GET
- 要调试post请求需要用到其他工具，例如postman 
- HTTP请求是无状态的，每一次的请求对于服务器和浏览器来说都是一样的

#### JVM
- Java 10编译器 能不能运行Java 8编译出来的文件，取决于Java 10 生成出来的文件版本，如果编译出来的.classd的版本是JVM 能够运行的（JVM不能够运行比自己版本搞的字节码文件），对应版本的Java就能够运行。


## SpringBoot 基础知识
- 对象
- - 需要对外提供服务的需要声明Bean，例如提供鉴权、查询等服务功能
- - - `Controller对象`则会通过声明`@Controller`从而完成Bean的声明,进行Bean装配
- - - `Service对象`则会通过`@Component`、`@Service`进行声明
- - 作为数据容器容纳数据的就不需要，一般放在`entity包`里面会比较容易处理
```java
public class User {
    Integer id;
    String name;
    String avatar;
    Instant createdAt;
    Instant updateAt;

    public User(Integer id, String name) {
        this.id = id;
        this.name = name;
        this.avatar = "";
        this.createdAt = Instant.now();
        this.updateAt = Instant.now();
    }

```



## SpringBoot应用
#### 从零新建SpringBoot
- 新建一个符合需求的结构 
```
$ mkdir -p src/main/java/hello
```
- 新建一个pom.xml,填充
```
$ vi pom.xml
```
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.2.2.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>spring-boot</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>spring-boot</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```
- 新建一个Controller和Application（根据示例文档）
##### 完成依赖
- 通过构造器完成，而非`@Resourc`、`@Autowired`等依赖注入的方式
```java
public class orderService {
    pricate UserService userService;
    @Ingect
    public OrderService(UserService userService){
        this.uesrService = userService;
    }
}
```
#### Inject 无法使用的时候
- 搜索Javax Inject Maven 添加后就可以使用
#### Bean生成
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

## ORM
#### ORM （Object Relationship Mapper 对象关系映射）
- SpringBoot 和 Database 是两个独立的进程 通过URL（JDBC）进行通信，ORM则是完成通信的任务
1. JPA（） --> 需要的SQL量很少
- Spring Boot Jpa 是 Spring 基于 ORM 框架、Jpa 规范的基础上封装的一套 Jpa 应用框架，可使开发者用极简的代码即可实现对数据的访问和操作。它提供了包括增删改查等在内的常用功能。
- Jpa 是一套规范，不是一套产品，那么像 Hibernate,TopLink,JDO 他们是一套产品，如果说这些产品实现了这个 Jpa 规范，那么我们就可以叫他们为 Jpa 的实现产品。
2. MyBatis
- 需要装配MySql-connect-java Maven
#### MYSQL
1. 安装MYSQL
2. Docker Mysql
- 全隔离，不对本机进行影响
- 对本机的环境配置没有要求  

## 通过Mybatis完成SpringBoot链接DB
- 完成Mybatis和Spring的装配需要`SqlSessionFactory` 和 `mapper接口`
- SpringBoot Mybatis Maven 
```java
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```
####  新建一个接口存放关系映射的Mapper(最好新开一个包存放，便于管理)
- - 一定要记得加`@Mapper`,否则会编译不通过
- - `#{}` ==> `@Param("")` 相对应，不能够错！！
```java
@Mapper
public interface CityMapper {

  @Select("SELECT * FROM CITY WHERE state = #{state}")
  City findByState(@Param("state") String state);

}
```
- 把Mapper注入到需要完成依赖的Bean里面，`@Inject` 是关键！，也可以用`@Autowired`代替啦
- userMapper 无法创建实例，因为UserMapper是一个接口，但是SpringBoot能够自动管理，直接装配成Bean
```java
    public class UserService {
        
        @Inject
        public UserService(UserMapper usermapper) {
            this.userMapper = userMapper;
        }
        
        public User getUserById( Integer id){
            return userMapper.findUserById(id);
        }
    }
```
- 这个时候userMapper的这个Bean是不能完成自动装配的，因为找不到UserMapper这个类存在。Mybatis能够自动检测`DataSource`，然后自动生成`SqlSessionFactory`,不需要额外装配。
- - 在基础的 MyBatis 用法中，是通过 `SqlSessionFactoryBuilder`来创建 `SqlSessionFactory`的。 而在 MyBatis-Spring 中，则使用 SqlSessionFactoryBean 来创建。
- 创建DataSource（Spring的xml配置）
- - 在resources里创建`application.properties`
- - 添加

```java
spring.datasource.url = jdbc：mysql：// localhost / test
 spring.datasource.username = dbuser
 spring.datasource.password =
 dbpass spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver
 mybatis.config-location =  classpath:db/mybatis/Mybatis-config.xml

```
- Mybatis 配置
```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <settings>
        <setting name="autoMappingUnknownColumnBehavior" value="WARNING"/>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <setting name="jdbcTypeForNull" value="NULL"/>
    </settings>
    <environments default="development">
        <environment id="development">
            
            <transactionManager type="JDBC"/>
           
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/user"/>
                <property name="username" value="root"/>
                <property name="password" value="hana"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="db/mybatis/mapper/BlogMapper.xml"/>
    </mappers>

</configuration>

```

**https://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/**

## Controller
- 官例
```java
@RestController
public class UserController {
    private UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @RequestMapping("/")
    public User index() {
        return this.userService.getUserById(1);
    }

}
```

## 登录模块（Controller）

#### Spring MVC
-  模型model(javabean),  视图view(jsp/img)   控制器Controller(Action/servlet)  
- C存在的目的就是为了保证M和V的一致性
   当M发生改变时,C可以把M中的新内容更新到V中.
- **SpringMVC是Spring框架内置的MVC的实现，一个Spring内置的MVC框架**  
MVC框架，它解决WEB开发中常见的问题(参数接收、文件上传、表单验证、国际化、等等)，而且使用简单，与Spring无缝集成。   
支持 RESTful风格的 URL 请求 。  
采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性。
- 为了解决页面代码和后台代码的分离


##### 接口
- HTTP 只能返回字节流，而Java后端则会返回一个对象
```java
@Controller
public class AutoController {
    @GetMapping("/auth")
    public Object auth() {
        return new Result();
    }

    private static class Result {
        public String getStatus() {
            return "OK";
        }

        public boolean getIsLogin() {
            return false;
        }// -->private static 是无法返回的
    }
}
```
- - 如果`/auth`返回的是一个`String`的时候，视图就会在当前的环境中寻找一个名为`string`的视图，并尝试渲染
```java
@Controller
public class AutoController {
    @GetMapping("/auth")
    public String auth() {
        return "user";
    }
```
- ERROR
```
Servlet.service() for servlet [dispatcherServlet] in context with path [] threw exception [Circular view path [auth]: would dispatch back to the current handler URL [/auth] again. Check your ViewResolver setup! (Hint: This may be the result of an unspecified view, due to default view name generation.)] with root cause
```
- 解决：添加`@ResponseBody`--> 方法返回值限定在ResponseBody里面
```java
    @GetMapping("/auth")
    @ResponseBody
    public Object auth(ModelMap map) { // 获得返回的的数据、HTTP的HEAD 等等的数据
        return new Result();
    }
```
##### 用户登录接口
- 完成这个配置后，就建立安全框架，所有的请求都会进行拦截，都会进行安全处理
```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
}
```
- 访问根目录和`/home`的请求都全部通过
```java
@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
			.authorizeRequests()
				.antMatchers("/", "/home").permitAll()
```
根据需要进行调整
```java
                .antMatchers("/", "/auth/**").permitAll();
```
- SRCF 关闭. 对POST请求生效，关闭后才能够完成请求
```java
 protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable();
```
- 建立虚拟用户框架，使得除了建立的用户以外其他都是不合法的
```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
 @Bean
    @Override
    public UserDetailsService userDetailsService() {
        UserDetails user =
                User.withDefaultPasswordEncoder()
                        .username("user")
                        .password("password")
                        .roles("USER")
                        .build();

        return new InMemoryUserDetailsManager(user);
    }
}
```


- - ` User.withDefaultPasswordEncoder()`存在不安全的情况，只能在简单示例里使用
- - `UserDetailsService`
```
 * Locates the user based on the username.
  @param username the username identifying the user whose data is required.
```
```java
  public interface UserDetailsService {
  UserDetails loadUserByUsername(String username) throws UsernameNotFoundException; //输入用户名，就能够获得用户信息
}
 ```
 
---
- - 建立真实的用户框架
```java
@Service
public class UserService implements UserDetailsService {
    private Map<String, String> userPasswords = new ConcurrentHashMap<>(); //为了安全起见最好使用线程安全
    private BCryptPasswordEncoder bCryptPasswordEncoder;

    @Inject
    public UserService(BCryptPasswordEncoder bCryptPasswordEncoder) {
        this.bCryptPasswordEncoder = bCryptPasswordEncoder;
        save("Gigi", "111");
    }

    public void save(String username, String password) {
        userPasswords.put(username, bCryptPasswordEncoder.encode(password));
    }

    public String getPassword(String username) {
        return userPasswords.get(username);
    }
```
 
  Error : `PasswordEncode`
- - 密码需要加密：在用户使用的密码里面添加固定的密码加密，避免密码外泄
- - 加密是不可逆的
- - 加密必须是一致的
- - 不要自己设置加密算法
```
 * Implementation of PasswordEncoder that uses the BCrypt strong hashing function. Clients
```
在userService提供密码登录服务处，进行密码加密后再把密码写入数据库，下一次用户登录进行鉴权的时候，鉴权的是加密后的密码
```java
@Service
public class UserService implements UserDetailsService {
    private Map<String, String> userPasswords = new ConcurrentHashMap<>(); //为了安全起见最好使用线程安全
    
    private BCryptPasswordEncoder bCryptPasswordEncoder;//配置密码加密器

    @Inject
    public UserService(BCryptPasswordEncoder bCryptPasswordEncoder) {
        this.bCryptPasswordEncoder = bCryptPasswordEncoder;
        save("Gigi", "111");
    }

    public void save(String username, String password) {
        userPasswords.put(username, bCryptPasswordEncoder.encode(password));//把用户名和加密后的密码存储在UserMapper里面
    }

    public String getPassword(String username) {
        return userPasswords.get(username);
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        if (!userPasswords.containsKey(username)) {
            throw new UsernameNotFoundException(username + "不存在");
        }
        String encodePassword = userPasswords.get(username);

        return new org.springframework.security.core.userdetails.User(username, encodePassword, Collections.emptyList()); 
        //当被询问用户信息的时候，就会返回用户的名字和加密后的密码
    }
}
```

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
    @Autowired
    UserDetailsService userDetailsService;
    
    @Autowired
    public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(bCryptPasswordEncoder());  
        //在全局的环境下，让所有的密码都通过 `bCryptPasswordEncoder()` 进行加密
    }

    @Bean
    public BCryptPasswordEncoder bCryptPasswordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

---
- 用户框架建立并提供通过用户名读取用户信息的服务后，注入控制，使得鉴权的方法可以完成用户信息的核对。
```java
@Controller
public class AutoController {
    private UserDetailsService userDetailIsService;

    @Inject
    public AutoController(UserDetailsService userDetailIsService) {
        this.userDetailIsService = userDetailIsService;
    }
     UserDetails userDetails = userDetailIsService.loadUserByUsername(username);
      UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken
                (userDetails, password, userDetails.getAuthorities());  //鉴权，检验密码是否匹配该用户

    }
```


- 鉴权：判断信息和用户的信息是否一致,证明自己是自己
```java
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
 @Bean
 @Override
 public AuthenticationManager authenticationManagerBean() throws Exception {
        return authenticationManager();
    }
}
```

```
@Controller
public class AutoController {
  private AuthenticationManager authenticationManager;//管理鉴权
   @PostMapping("/auth/login")
    @ResponseBody
    public void login(@RequestBody Map<String, String> usernameAndPassword) {
     UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken
                (userDetails, password, userDetails.getAuthorities());  //鉴权，检验密码是否匹配该用户

        try {
            authenticationManager.authenticate(token); //完成鉴权的管理
            SecurityContextHolder.getContext().setAuthentication(token); //鉴别完成后，进行数据处理

        }catch (BadCredentialsException e){

        }
    }
```
- - `authenticationManager.authenticate();`出现鉴权失败
**添加` @Autowired`**解决
```java
public class AutoController {
 
  private AuthenticationManager authenticationManager;
    @Inject
    public AutoController( AuthenticationManager authenticationManager) {
        this.authenticationManager = authenticationManager;
        }
  ```
- - `AuthenticationManager`简介
```
/*
* Override this method to expose the {@link AuthenticationManager} from
	 * {@link #configure(AuthenticationManagerBuilder)} to be exposed as a Bean.
	 * @return the {@link AuthenticationManager}
	 * @throws Exception
	 */
	 @Bean
	 public AuthenticationManager authenticationManagerBean() throws Exception {
		return new AuthenticationManagerDelegator(authenticationBuilder, context);
	}
```
- - 管理鉴权，鉴权成功后处理信息
```
 @PostMapping("/auth/login")
    @ResponseBody
        public Object login(@RequestBody Map<String, Object> usernameAndPassword) {
        String username = usernameAndPassword.get("user").toString();
        String password = usernameAndPassword.get("password").toString();

        UserDetails userDetails = userDetailIsService.loadUserByUsername(username);
        UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken
                (userDetails, password, userDetails.getAuthorities());  //鉴权，检验密码是否匹配该用户
        try {
            authenticationManager.authenticate(token); //完成鉴权的管理
            SecurityContextHolder.getContext().setAuthentication(token); //鉴别完成后，进行数据处理
            //登录成功后
            User loggedInUser = new User(1, "Gigi");
            return new Result("ok", "登录成功", true, loggedInUser);
        } catch (BadCredentialsException e) {
            return new Result("fail", "密码不正确", false);
        }
    }
- - 验权：Authozation 证明自己是不是管理员



- PS 
1. SpringBoot 默认的pom.xml读取不到
`org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;`

添加
```
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-security -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
    <version>2.3.1.RELEASE</version>
</dependency>
```

打包（不去确定是否需要）`mvn package`

###### Java对象（Bean）
- private 
- - message
- - status
- 如果一个Bean有一个`getStatus()`，就会认为这个Bean有一个`Status`属性
###### Jason 显示
```java
 private static class Result {
        String status;
        String msg;
        boolean isLogin;
        Object Data;

        public String getStatus() {
            return status;
        }

        public String getMsg() {
            return msg;
        }

        public boolean isLogin() {
            return isLogin;
        }
```

- 网页jason显示出的内容，取决于getter和setter，而非`String status`、`boolean isLogin`等字段

## 维持登录状态
##### cookie 基础 （http rfc）
- HTTP请求是无状态的，每一次的请求对于服务器和浏览器来说都是一样的，通过Cookie维持状态
- 浏览器看到服务器在ResponseHeader响应中返回set-cookie的时候，就会在之后的每一次请求都把cookie的内容放在RequestHeader里面 
- 用户信息保存在cookie，所以 cookie的内容包含用户信息，每一次包含cookie的请求都表示是该用户的行为
- cookie和浏览器域名是相绑定的，cookie会规定在相应的域名`/path`请求才能够携带cookie 
- cookie的httponly 表示cookie只有能够被浏览器发送 
##### cookie 维持登录状态
- 当没有登录的时候，  
`String userName = SecurityContextHolder.getContext().getAuthentication().getName();
`userName 就会得到  
`anonymousUser`

## 用户登录与信息储存
- Database
- - id 为主键,且自动生成数据，从`1`开始
- - avatar 头像为Url，所以长度比较长
```
create table user(id int primary key auto_increment,
username varchar(10),
encrypted_password varchar(100),
avatar varchar(100),
created_at datetime,
updated_at datetime)
```
- Database 对应Mapper 的配置
```java
@Mapper
public interface UserMapper {
@Select("SELECT * FROM user WHERE username = #{username}")
    User findUserByUsername(@Param("username") String username);

    @Select("Insert into user(username,encrypted_password,created_at,updated_at)" +
            "values(#{username}, #{encrypted_password}, now(),now())")
    void save(@Param("username") String username, @Param("encrypted_password") String encryptedPassword);
}
```
- Mybatis 的配置（位于application）

```
spring.datasource.url = jdbc:mysql://localhost:3306/user
spring.datasource.username = root
spring.datasource.password = hana
dbpass spring.datasource.driver-class-username = com.mysql.cj.jdbc.Driver
mybatis.configuration.mapUnderscoreToCamelCase = true
```
- - 需要添加`mybatis.configuration.mapUnderscoreToCamelCase = true
`使得数据库能够识别到Java的驼峰命名，从而避免数据库和浏览器数据出现不一致的问题
----
- - userMapper 代替了userDetailServical的功能，同样是给一个username就获得用户的信息
```java
@Service
public class UserService implements UserDetailsService {
   private UserMapper userMapper;
    @Inject
    public UserService(BCryptPasswordEncoder bCryptPasswordEncoder,UserMapper userMapper) {
        this.bCryptPasswordEncoder = bCryptPasswordEncoder;
        this.userMapper = userMapper;
          save("Gigi","111");
    }

    public void save(String username, String password) {
        userMapper.save(username,bCryptPasswordEncoder.encode(password));
    }
  public User getUserByUsername(String username) {
        return userMapper.findUserByUsername(username);
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = getUserByUsername(username);
        if (user == null) {
            throw new UsernameNotFoundException(username + "不存在");

        }
        return new org.springframework.security.core.userdetails.User(username, user.getEncryptedPassword(), Collections.emptyList());
    }
}
```

```java
@Controller
public class AutoController {
    private AuthenticationManager authenticationManager;//管理鉴权
    private UserService userSe
    
  @PostMapping("/auth/login")
    @ResponseBody
    public Object login(@RequestBody Map<String, Object> usernameAndPassword) {
        String username = usernameAndPassword.get("username").toString();
        String password = usernameAndPassword.get("password").toString();

        UserDetails userDetails;
        try {
            userDetails = userService.loadUserByUsername(username);
        } catch (UsernameNotFoundException e) {
            return new Result("fail", "用户不存在", false);
        }

        UsernamePasswordAuthenticationToken token = new UsernamePasswordAuthenticationToken
                (userDetails, password, userDetails.getAuthorities());  //鉴权，检验密码是否匹配该用户
        try {
            authenticationManager.authenticate(token); //完成鉴权的管理
            SecurityContextHolder.getContext().setAuthentication(token); //鉴别完成后，进行数据处理并保存
            //登录成功后
            return new Result("ok", "登录成功", true, userService.getUserByUsername(username));
        } catch (BadCredentialsException e) {
            return new Result("fail", "密码不正确", false);
        }
    }
}
```

## 序列化返回时不返回某些信息
- 序列化：
- Spring 序列化方法：jackson
- 使用`@JsonIgnore`

```java
public class User {
    Integer id;
    String username;
    @JsonIgnore
    String encryptedPassword;
    String avatar;
    Instant createdAt;
    Instant updatedAt;
```

## 登录退出接口
- 通过`SecurityContextHolder.clearContext();` 完成


## 测试
- 自动化
- 节省时间
- 测试水平 
1. 单元测试
- 服务内部某个模块的测试
- 简单快速，不需要对代码进行打包、部署
- - Eg：测试`UserService`里面的`save()`服务是否成功

```java
 @Test
    public void testSave() {
        //调用userService
        //验证userService将请求转发给userMapper


        //BCryptPasswordEncoder会因为输入的是Mock，所以加密后返回的回是null，需要额外给条件
        //given
        Mockito.when(mockEncoder.encode("MockPassword")).thenReturn("MockEncodedPassword");
        //when:
        userService.save("MockUser", "MockPassword");
        //then:
        Mockito.verify(mockMapper).save("MockUser", "MockEncodedPassword");
    }
```

2. 集成测试
- 黑盒测试（mock）：测试服务对外的功能，把整个测试装配好后，测试对外接口的功能
- 安全可靠，但是慢且复杂
- - Eg: 测试没有登录状态下用户认证情况
```java
    @Test
    void returnNotLoginByDefault() throws Exception {
        mockMvc.perform(get("/auth"))
                .andExpect(status().isOk())
                .andExpect(new ResultMatcher() {
                    @Override
                    public void match(MvcResult mvcResult) throws Exception {
                        Assertions.assertTrue(mvcResult.getResponse().getContentAsString().contains("用户没有登录"));
                        //System.out.println(mvcResult.getResponse().getContentAsString()); //用于查看返回内容
                    }
                });
    }


```


3. 冒烟测试（smoke test）
- 应用开发完成后进行最简单的检查（能够完成访问等简单的测试），初步判断服务可用


```java
import org.junit.jupiter.api.Test;

public class SmokeTest {
    @Test
    public void test(){

    }
}
```
- `@Test`默认的`jupiter`是`JUnit 5 `默认的测试引擎 

4. 回归测试
- 开发的新功能，避免旧的功能出问题
- 代码出错的可能减少，趋于稳定


#### Maven
 - 单元测试
 ```
 <plugin>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.22.2</version>
        </plugin>
```
- 集成测试
```
        <plugin>
            <artifactId>maven-failsafe-plugin</artifactId>
            <version>2.22.2</version>
        </plugin>
```
- - 只想单独运行单元测试或集成测试
1. verify 运行检查确保代码有效
2. integretion-test  相比于 verify位于maven生命周期的前几个（运行maven 的范围比 verify少）
3. 单独运行单元测试

```java       
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
                <configuration>
                    <excludes>
                        <exclude>
                            **/*IntegrationTest
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        
```
```
mvn clean test
```

4. 单元测试和集成测试分开进行

```
            <plugin>
                <artifactId>maven-failsafe-plugin</artifactId>
                <version>2.22.2</version>
                <configuration>
                    <includes>
                        **/*IntegrationTest
                    </includes>
                </configuration>
            </plugin>
```
```
mvn clean verify
```

#### @Before、@Test
- 理解
```java
    @BeforeEach
    void setUp() {}

   @Test
   void test1(){}

    @Test
    void test2(){}
    //要想调用teat1这个实例方法，就要有一个实例来调用
    AutoControllerTest testInstance = new AutoControllerTest() ;
    testInstance.test1();
    //没调用一个实例方法，就会自动创建一个实例对象来调用 。因为在两个实例测试的过程中，如果共享一个实例，可能会共享一些状态、数据导致偏差。
    // 而两个不同的实例能够保证数据状态的独立
    //EG
    AutoControllerTest testInstance1 = new AutoControllerTest() ;

    testInstance1.test1();
    AutoControllerTest testInstance2 = new AutoControllerTest() ;
    testInstance2.test2();

    //而BeforeEach 就是在调用实例方法前都会调用一次@BeforceEach里面的方法
    AutoControllerTest testInstance1 = new AutoControllerTest() ;
    testInstance1.setUp();
    testInstance1.test1();
    AutoControllerTest testInstance2 = new AutoControllerTest() ;
    testInstance1.setUp();
    testInstance2.test2();
```

- @BeforeEcah
 ```
 @BeforeEach
    void setUp() {
            mockMvc = MockMvcBuilders
                 .standaloneSetup(new AutoController(authenticationManager,userService))
                 .build();//测试的是`AutoController()`这个方法
    }
        @Test
    void returnNotLoginByDefault() throws Exception {
        mockMvc.perform(get("/auth"))
                .andExpect(status().isOk())
                .andExpect(new ResultMatcher() {
                    @Override
                    public void match(MvcResult mvcResult) throws Exception {
                        Assertions.assertTrue(mvcResult.getResponse().getContentAsString().contains("用户没有登录"));
                        //System.out.println(mvcResult.getResponse().getContentAsString()); //用于查看返回内容
                    }
                });
    }

```


#### 实现
- `test/java/hello/service`和`main/java/hello/service` 的类都会处于`pavkage hello.service`里面，里面的包级私有`private`的方法就能够共用，从而测试出非public的方法，且不被包外的方法访问到
1. 引入Junit5 mokito 的Maven
2. 构建测试需要用到的虚拟对象
```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    BCryptPasswordEncoder mockEncoder;
    }
```
3. 通过`Mockito.when().thenReturn();`方法来构建虚拟的对象，或者控制条件
```java
 Mockito.when(userService.loadUserByUsername("MockUser"))
                .thenReturn(new User("MockUser", bCryptPasswordEncoder
                        .encode("MockPassword"), Collections.emptyList()));
        Mockito.when(userService.getUserByUsername("MockUser")).thenReturn(new com.entity
                .User(1213, "MockUser", bCryptPasswordEncoder
                .encode("MockPassword")));
```

```java
 //BCryptPasswordEncoder会因为输入的是Mock，所以加密后返回的回是null，需要额外给条件
        Mockito.when(mockEncoder.encode("MockPassword")).thenReturn("MockEncodedPassword");
       
```
4 .通过断言判断结果是否正确
- Assertions.assertThrows
```java
    @Test
    public void throwExceptionWhenUserNoFount() {
        Mockito.when(mockMapper.findUserByUsername("MockUser")).thenReturn(null);
        Assertions.assertThrows(UsernameNotFoundException.class, new Executable() {
          @Override
          public void execute() throws Throwable {
              userService.loadUserByUsername("MockUser");
          }
      });
    }        
    // 断言 userService.loadUserByUsername("MockUser") 调用 一定会丢出  UsernameNotFoundException.class异常，
```
- Assertions.assertEquals
```java
 @Test
    public void returnUserDetailWhenUserFound(){
        Mockito.when(mockMapper.findUserByUsername("MockUser")).thenReturn(new User(1212,"MockUser","MockEncodedPassword"));

        UserDetails userDetails = userService.loadUserByUsername("MockUser");
        Assertions.assertEquals("MockUser",userDetails.getUsername());
        Assertions.assertEquals("MockEncodedPassword",userDetails.getPassword());
    }
```
## Question
1. Post 和 Get 的区别
- HTTPRequest 请求前几个字符 是POST 还是GET
2. Pose Get  发送过程


## 遇到的问题
1. 问题：在测试的时候，需要测试SpringMvc容器返回的结果是否正确。然而返回内容的中文为乱码。
原因：`getResponse().getContentAsString())`的getContentAsString())使用到`characterEncoding`这个方法，而这个方法为` private String characterEncoding = "ISO-8859-1";`而非utf-8，所以出现乱码

```java
public String getContentAsString() throws UnsupportedEncodingException {
        return this.characterEncoding != null ? this.content.toString(this.characterEncoding) : this.content.toString();
    }
```
```java
@Test
    void returnNotLoginByDefault() throws Exception {
        mockMvc.perform(get("/auth"))
                .andExpect(status().isOk())
                .andExpect(new ResultMatcher() {
                    @Override
                    public void match(MvcResult mvcResult) throws Exception {
                        //Assertions.assertTrue(mvcResult.getResponse().getContentAsString().contains("用户没有登录"));
                        System.out.println(mvcResult.getResponse().getContentAsString()); }
                });
    }
```




解决：  
在被测试的方法添加` @GetMapping(value = "/auth",produces = "application/json; charset=utf-8" )`  
原为：`@GetMapping("/auth")`


2. 遇到某个类找不到可能是因为一个库版本过久引起的时候（Error为某一个类是不安全的类）
解决：
```
mvn dependency:tree > a.txt
```
```
vi  a.txt
```
查找到该库，找到引入该库的包，并进行忽略该库的操作
```
<scope>……</scope>
 <exclusions>
                <exclusion>
                    <groupId>该库</groupId>
                    <artifactId>  </artifactId>
                </exclusion>
            </exclusions>
```
然后添加该库新的版本
            



### github GI测试大型代码
- 添加travis-ci
- - 在项目里面添加 `vi .travis.yml`
- - script 使用什么命令运行 ：mvn verify
- 添加好后就push到github上面，并打开travis-ci
    **https://travis-ci.org/**,先同步账户，然后找到自己的项目，并把开关打开，这样项目就能够被扫描到
- Ci执行
- - 因为有`.tracis.yml`,所以ci在之后的项目push之后就会自动触发，进行检测(文件名应该是为`.tracis.yml`才能触发吧……)

##### jenkins测试 实现
- docker  
- - 关闭docker jenkins 之后，重启只需要把新建的命令行再输入一次就可以了
- - `docker run --rm -p 8081:8080  --name = jenkins-data -v `pwd`/jenkins-data:/var/jenkins_home jenkinszh/jenkins-zh:latest`(国内版本的jenkins更容易下载插件)

（记得给一个目录来保存！！！）
- - 运行后会获得一个密码
`e3a72d076e38478fb8aaeb013140a5ed
`
- - 打开`127.0.0.1:`，输入密码，选择需要安装的插件（选择的插件越多，运行的时间就越长），选择好后进行安装
- - 镜像：https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json
- - 重启：`restart`(之后通过镜像下载就更快)
- - 新建，选择自由风格
- - 选择github项目后把github上的库的链接粘贴下来（最好是用http的链接，因为docker是没有配置到ssh的
，但是可以再build environment 那里额外配置）
- - 在build 的地方填写执行shell（用什么命令执行），Eg:`mvn test`
- - 直接build 会失败，因为docker 是一个全新的容器，没有安装maven，所以需要绕过Maven或者安装Maven。 Eg:
```
Maven wrapper
在该项目里运行
mvn -N io.takari:maven:0.7.7:wrapper

```
运行后增加的文件里面包含了下载maven的路径，就能够在docker里面下载一个maven
- - push后，记得要改变项目的运行命令，Eg：`./mvnw test`




####  在github上新建一个库(把在本地写好的代码传到一个新建的github仓库里面)
- - 新建好后复制，然后打开想要储存的文件夹，`git clone`
- - clone 完后，进入该文件夹，`cp -r ~/本地代码的地址/* .`
- - 删掉复制后的`.iml`文件（IDE的有效文件）
- - 在`.gitignore`里添加`target/` 


#### 集成测试
- 建立测试用的数据库

- - 通过`exec-maven-plugin` 完成。因为整个过程都是在docker里面按成的，所以执行的是`exec:exec`
- - 通过flyway来完成数据库的建表
https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html

## 概念
- TDD 先写测试后写业务
- Dao
- - Controller（参数验证）控制service（处理业务），service控制Dao（访问数据库）

## 博客
#### 数据库设置
- Mapper 不能够完成复杂的SQL，需要Mybatis 通过配置来完成动态SQL完成
##### Mybatis
- config 和 configuration 不能够同时使用
- 位于reasource文件夹下的文件路径可以为：`classpath:`Eg
```
位于：reasource/db/mybatis/mybatis-config.xml
路径为:classpath:db/mybatis/mybatis-config.xml`
```
- ResultMap 要用全限定类名
- Mybatis 自动配备Sqlsession
- mapper 的路径从reasource 开始
```
位于:reasource/db/mybatis/mapper/BolgMapper
路径为:db/mybatis/mapper/BlogMapper.xml
```


## Docker
- docker是一个独立的运行容器，区别于host宿主机，两者是不互通的（端口即使相同，也不能访问）。
- `-p`(port) 80:8080 `host p:docker p` 用于两者互通
- `-v`(volume，卷，文件系统) /path1:path2 `host path:docker path`
```
/root:/app:ro 
在docker /app 里可以访问到hose /root 里面的文件，但是只有read-only 的权限
```

- `-e`(environmen variable)为docker 提供环境变量

#### 从零搭建docker 应用 
- Dockerfile Docker的数据库，运行的说明书
- - Dockerfile 的位置和docker 启动路径要一样，否则会找不到
```
MAC-2 in ~/IdeaProjects/SpringBoot-Demo on git:master x [17:40:05] C:1
$ docker build . -t my-docker-app
unable to prepare context: unable to evaluate symlinks in Dockerfile path: lstat /Users/jinhua/IdeaProjects/SpringBoot-Demo/Dockerfile: no such file or directory

```
- - FROM 要启动的应用的tag
- - RUN mkdir /docker工作路径
- - docker 里`alpine` 表示微缩
- - COPY 把文件从本地目录拷贝到docker说明书里面
- WORKDIR docker的工作路径，因为docker 是一个虚拟的容器，所以工作路径可以自定义
- - EXPOSE Docker 搭建应用所运行的端口
- - 通过 docker command 启动 docker
- 镜像 把dockerfile 变成一个镜像，才能够随时启动
- Docker 运行
- - `docker build . -t my-docker-app` `-t`后加应用tag来辨认
- - `docker images `可以查看到刚才新建的应用
- - `docker run -it -p 8081:8080 my-docker-app`
- - 直接docker run 会出现↓，需要`mvn clean package` 然后`java -jar target/ .jar`进行测试是否成功，如果不行可以再试一下clean，然后再运行jar 来查看。成功后重新 bulid、run
```
The dependencies of some of the beans in the application context form a cycle:

┌─────┐
|  userService defined in URL [jar:file:/dockerworkdir/spring-boot-0.0.1-SNAPSHOT.jar!/BOOT-INF/classes!/com/service/UserService.class]
↑       ↓
|  webSecurityConfig (field org.springframework.security.core.userdetails.UserDetailsService com.configuration.WebSecurityConfig.userDetailsService)
└─────┘

```
- `docker exec -it 容器ID sh` 可以进入到容器里面，查看容器的情况
- - docker 容器里面的localhost 和 host 里面的localhost 是不一样的，所以docker 容器访问host 的其他服务（mysql……） 需要使用url，不是localhost，而是本机的局域网IP
- - `$ ifconfig`,找到`en0`（以太网的网卡名）里面的`inet`里的(EG`000.000.0.0`)
- docker 访问host 的其他服务（mysql）
- - 改变服务的URL，把localhost 改为 本机的局域网Ip
```
spring.datasource.url=jdbc:mysql://localhost:3306/user
改为
spring.datasource.url=jdbc:mysql://000.000.0.0:3306/user
```
## 阿里云云台
#### 购买服务器并步置
- 大多数都可以默认设置，选择好SSH和地区就好
- 创建成功后，复制pub的地址
- `ssh root@pub地址`
- - 安装Ubuntu`$ sudo apt-get update`,但是因为已经在`root`里，所以可以直接`apt-get update`
- - `$ apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common`
- - `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
- - `apt-key fingerprint 0EBFCD88`
- - `add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"`
- - `apt-get update`
- - `apt-get install docker-ce docker-ce-cli containerd.io`
https://docs.docker.com/engine/install/ubuntu/
- 阿里云安全设置
- - 设置实例安全组 添加的授权对象最好为`0.0.0.0/0`,所有人都可以访问。
- 设置服务器的权限
- - 利用 chmod 可以藉以控制文件如何被他人所调用。
- - `chmod 777 jenkins-data`
- - `chmod 777 jenkins-m2`
- - `chmod 777 .`
1. a,b,c各为一个数字，分别表示User、Group、及Other的权限。
r=4，w=2，x=1
若要rwx属性则4+2+1=7；
若要rw-属性则4+2=6；
若要r-x属性则4+1=5。
- 给docker 设置镜像提速
- - `vim /etc/docker/daemon.json`，添加
```
{
 "registry-mirrors": ["https://registry.docker-cn.com"]
}
```
- 安装docker jenkins (weweave)
- - `mkdir jenkins-data` 存放data
- - `mkdir Jenkins-m2` 存放maven构建中缓存的数据,避免每次容器重启的时候还要去下载maven的插件和相关的构建
- - `docker run  -it -p 8081:8080 -v `pwd`/jenkins-data:/var/jenkins_home -v `pwd`/jenkins-m2:/var/jenkins_home/.m2 jenkins/jenkins:lts`
- 网页登录jenkins，下`git`，`Pipeline`、`Pipeline:Stage view`和`Docker`
- 新建 多分支流水线项目（只有docker 插件下载成功才能够在新建项目里面出现）
- - 把项目仓库的地址复制到jenkins `Barnch Source`(分支源) git 

- jenkinsfile 本质就是 Gorval
- docker registry
- - docker官方提供给用户私有仓库镜像，从而使得用户能够部署自己的私有仓库
- 指定的url为https的时候会被认为是不安全的，最简单的解决方法就是在docker的配置里面添加相应的url（服务器的ip地址），才可以在本地docker中push。同样的，也需要在部署的jenkins里的docker中修改`insecure-registried`


#### 问题
1. root@47.107.252.72: Permission denied (publickey).  
解决：有可能是网上的改sshd 配置等操作外，也可以试一下去更改服务器实例的密码
2. jenkins 下载插件超时  
解决：可以尝试用国内jenkins版本运行，添加更换镜像。还可以把`scm签出重试次数`调大一点试试






- 阿里云更改过系统之后，需要把know_hosts 对应地址的部分删掉
 

## 先放在这里
- getmapper 非必传的参数设置
- @RequestParam(value = "userId",required = false) Integer userId

如果您想知道应用程序在哪个HTTP端口上运行，请通过键获取属性local.server.port

#### 设置Maven 镜像 （maven aliyun mirror repositories）
1. 写在Home目录/m2/setting.xml 对所有项目生效（全局）
2. 写在pom.xml 里面

#### Clone 前端代码注意
- clone 前端代码后，要检查baseURL进行清空更改，否则链接上后返回请求的是原来的服务器，而非本机。
```
更改为只剩下"//"
```
