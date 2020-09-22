---
title: "Spring"
date: 2020-09-01T11:37:47+08:00
draft: false
toc: false
images:
tags: [SpringMvc, Spring Security,]
---



## Spring 容器原理
- Ioc 容器的事实标准
- Ioc (Inverse of Control控制反转) :只需要告诉容器对象的依赖关系，容器就会自动完成依赖和Beans的生成，通过依赖注入完成依赖，整个过程就是控制反转
- Java对象是Bean
- 当A对象必须使用B对象才能完成自己的工作的时候，就是A依赖B 

#### Spring 注解
- `@RestController` （Spring4.0）
- - 继承 `@Controller`，在SpringMvc当中使用@Controller来标识当前类是一个控制器Servlet。
- - 包含@Target(value=TYPE)  @Retention(value=RUNTIME)、@Documented 、@Controller 、@ResponseBody  
- `@RequestMapping`，提供路由信息，告诉Spring 任何来自”/“路径的HTTP请求都会被映射到home 方法，@RestController注解告诉Spring 以字符串的形式渲染结果，并直接返回给调用者。
>- 两者都是SpringMvc 的注解，不是SpringBoot特定部分
- `@EnableAutoConfiguration`
- - 告诉SpringBoot 根据添加的Jar 依赖猜测配置Spring。 而Spring-boot-start-web添加了Tomcat 和SpringMvc ，所以auto-configutation 会嘉定你正在开发一个web应用并对Spring 进行设置。
- - 如果有不想自动配置的类可以通过 来禁止使用
>- @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
- `@Configuration`
- - @Configuration注解该类，等价 与XML中配置beans；用@Bean标注方法等价于XML中配置bean
- `@SpringBootApplication` ==  @Configuration ， @EnableAutoConfiguration 和 @ComponentScan 
- - 能够自动在Application 中完成配置
- `@ResponseBody`
- - 表示该方法的返回结果直接写入HTTP response body中
- - 一般在异步获取数据时使用，在使用@RequestMapping后，返回值通常解析为跳转路径，加上
`@responsebody`后返回结果不会被解析为跳转路径，而是直接写入HTTP response body中。比如
异步获取json数据，加上@responsebody后，会直接返回json数据。
- `@Resposotory`诗句持久层DAO
- `@Component`
- - 泛指组件，当组件不好归类的时候，可以使用这个注解进行标注。一般公共的方法会用上这个注解。非Controller、Service、DAO部分的注解
- `@Service`
- - 一般用于修饰service层的组件
- `@ComponentScan`
- - 表示将该类自动发现扫描组件。个人理解相当于，如果扫描到有@Component、@Controller、@Service等这些注解的类，并注册为Bean，可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。可以自动收集所有的Spring组件，包括@Configuration类。我们经常使用@ComponentScan注解搜索beans，并结合@Autowired注解导入。如果没有配置的话，Spring Boot会扫描启动类所在包下以及子包下的使用了@Service,@Repository等注解的类。
- `@AutoWired`
- - byType方式。把配置好的Bean拿来用，完成属性、方法的组装，它可以对类成员变量、方法及构
造函数进行标注，完成自动装配的工作。
- - 当加上（required=false）时，就算找不到bean也不报错。
- `@RequestParam`
- - 用在方法的参数前面。
- `@PathVariable`
- - 路径变量。
- `@ControllerAdvice`
- - 包含@Component。可以被扫描到。
- - 统一处理异常。
- `@ExceptionHandler（Exception.class）`
- - 用在方法上面表示遇到这个异常就执行以下方法。
```java
/**
 * 全局异常处理
 */
@ControllerAdvice
class GlobalDefaultExceptionHandler {
    public static final String DEFAULT_ERROR_VIEW = "error";
 
    @ExceptionHandler({TypeMismatchException.class,NumberFormatException.class})
    public ModelAndView formatErrorHandler(HttpServletRequest req, Exception e) throws Exception {
        ModelAndView mav = new ModelAndView();
        mav.addObject("error","参数类型错误");
        mav.addObject("exception", e);
        mav.addObject("url", RequestUtils.getCompleteRequestUrl(req));
        mav.addObject("timestamp", new Date());
        mav.setViewName(DEFAULT_ERROR_VIEW);
        return mav;
    }
}
```
- `@value`
- - 读取application.properties里面的配置
- - 使用@Value的类如果被其他类作为对象引用，必须要使用注入的方式，而不能new。
- - 业务类使用类型注入，注入的是父类接口的类型，因为父类类型可以指向子类
```
# face++ key
face_api_key = R9Z3Vxc7ZcxfewgVrjOyrvu1d-qR****
face_api_secret =D9WUQGCYLvOCIdsbX35uTH********
```
```
 @Value("${face_api_key}")
    private String API_KEY;
 
    @Value("${face_api_secret}")
    private String API_SECRET;
```

[https://blog.csdn.net/weixin_40753536/article/details/81285046?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param]


## Spring Security
- 出于安全目的，Spring Security不会直接使用UserDetail实现。他们简单地存储用户信息，稍后封装到Authentication中的对象。这允许非安全相关的用户信息(如电子邮件地址，电话号码等)存放在方便的地方。
- SpringSecurity里面有UserDetailService接口，里面通过使用UserDetails这个类来实现`loadUserByUsername（String usernme）`方法。
```
Implementations are not used directly by Spring Security for security purposes. They
 * simply store user information which is later encapsulated into {@link Authentication}
 * objects. This allows non-security related user information (such as email addresses,
 * telephone numbers etc) to be stored in a convenient location.
 ```


#### 
- SecurityContextHolder是spring security最基本的组件。用于存储安全上下文（security context）的信息。
- 当前操作的用户是谁，该用户是否已经被认证，他拥有哪些角色权限等这些都被保存在SecurityContextHolder中。
- 匿名用户
>- 对于匿名访问的用户，Spring Security 支持为其建立一个匿名的 AnonymousAuthenticationToken 存放在 SecurityContextHolder 中，这就是所谓的匿名认证。这样在以后进行权限认证或者做其它操作时我们就不需要再判断 SecurityContextHolder 中持有的 Authentication 对象是否为 null 了，而直接把它当做一个正常的 Authentication 进行使用就 OK 了。
 #### Authentication
 - Authentication是Spring Security方式的认证主体。
 - 是一个身份验证请求或者已鉴权的principal 的token
 - - 一旦通过AuthenticationManager 的验权，就会被储存在一个（thread-local）【被 `<SecurityContext> `被管理的`<SecurityContextHolder>`里】被身份验证机制（authentication mechanisms）所使用。--> SecurityContextHolder默认是使用ThreadLocal实现的，这样就保证了本线程内所有的方法都可以获得SecurityContext对象。
 --> 一个显示的身份验证，不能够被SpringSecutity 身份管理器（authentication mechanisms）以外的方法使用。
 >- authentication mechanisms -> 用于创建鉴权实例并使用

- Code: `SecurityContextHolder.getContext().setAuthentication(anAuthentication);`
- 默认返回的对象是UserDetails实例，其中包含了username，password和权限等信息
```
- getAuthorities()，权限信息列表，默认是GrantedAuthority接口的一些实现类，通常是代表权限信息的一系列字符串。
- getCredentials()，密码信息，用户输入的密码字符串，在认证过后通常会被移除，用于保障安全。
- getDetails()，细节信息，web应用中的实现接口通常为 WebAuthenticationDetails，它记录了访问者的ip地址和sessionId的值。
- getPrincipal()，最重要的身份信息，大部分情况下返回的是UserDetails接口的实现类，也是框架中的常用接口之一。

```

#### AuthenticationManager
- `AuthenticationManager`（接口）是认证相关的核心接口，也是发起认证的出发点，因为在实际需求中身份认证的方式有多种，一般不使用AuthenticationManager，而是使用AuthenticationManager的实现类ProviderManager ,
- `ProviderManager`内部会维护一个List<AuthenticationProvider>列表，存放多种认证方式，实际上这是委托者模式的应用（Delegate）。
- 也就是说，核心的认证入口始终只有一个：AuthenticationManager，不同的认证方式对应不同的AuthenticationProvider。
认证流程
1、通过过滤器过滤到用户请求的接口，获取身份信息（假如有多个认证方式会配置provider的顺序）

2、一般将身份信息封装到封装成Authentication下的实现类UsernamePasswordAuthenticationToken中

3、通过AuthenticationManager 身份管理器（通过配置找到对应的provider）负责验证这个UsernamePasswordAuthenticationToken

4、认证成功后（认证逻辑一般在service中），AuthenticationManager身份管理器返回一个被填充满了信息的（包括上面提到的权限信息，身份信息，细节信息，但密码通常会被移除）Authentication实例。

5、SecurityContextHolder安全上下文容器将第2步填充了信息的UsernamePasswordAuthenticationToken，通过SecurityContextHolder.getContext().setAuthentication(…)方法，设置到其中来建立安全上下文（security context)。

## Spring 事务抽象的核心接口
- jar:springframework:spring-tx
#### latformTransactionManager接口
-  getTransaction(TransactionDefinition definition)
- -  
- 事务的隔离级别和底下的数据库的隔离级别是对应的
- 真正执行事务隔离的是底层的数据库
- 事务个隔离可以通过编译的时候更改，但是不一定有用，因为真正实现的是数据库
#### 声明事务
- 做法：使用`@Transactional`注解。表明需要事务支持，基于AOP实现
- - 注解是属于Spring，而不是javax