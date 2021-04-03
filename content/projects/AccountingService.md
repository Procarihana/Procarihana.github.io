---
title: "AccountingService"
date: 2020-08-10T20:48:22+08:00
draft: false
toc: false
images: 
tags: [SpringBoot,Mybatis,数据结构,数据模型转换,Shiro,]
---

## `.ignore`放什么
1 `*.iml` 是 intellij idea的工程配置文件，里面是当前project的一些配置信息。.Idea存放项目的配置信息，包括历史记录，版本控制信息等。
2. `.idea/`
3. `target/`
## 项目结构和模型转换
1. external 用于存放外部团队的接口和服务，然后再Manager里面放。在接口处写注析。
2. exception 异常处理
3. config  存放MybatisBean等等的Bean
4. Controller 存放SpringBoot对外接口（API接口）
- 不要有过多的处理逻辑
- 参数校验越早做越好
5. manager 组合各个业务的逻辑 先写接口后写实现
6. Dao 
7. Converter 用于转换 Moudle的common向service等等的转换但是不能够用于Http、数据库、IO等操作
8. Service 部分是给前段展示的内容，不能直接返回密码（加密后的还是可以的）等内容

## 添加Maven 
- 通过Maven getting start

## SpringBoot
#### Spring boot 开发四大步
- 添加相应依赖
- - SpringBoot 引入
- - - 基础Maven引入：<parent>、spring-boot-starter-web、plugin
- 添加相应注解
- - 添加Appliation类作为SpringBoot入口，并添加注解`@SpringBootApplication`
- - 添加Controller类，并添加注解`@RestController`
> - `@RestController` == `@Controller` + @`ResponseBody`mapping
- 编写代码
- 添加相应配置
- - 在`resources`里添加`application.properties`或`application.yml`都可以更改配置
```
server:
  port: 8000(注意是冒号加空格)
```
https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html

## Mybatis
- 通过Docker 运行mysql
```java
docker run -d --name AccountingService-mysql -v /`pwd`/MysqlDatabase:/var/mysql/conf.d  -v /`pwd`/MysqlDatabase:/var/log/mysql -v /`pwd`/MysqlDatabase:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=hana -p 3306:3306 mysql:8.0
```
- 设置mysql
```java
create table `accounting_userInfo`(
`id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
`username` varchar(64) NOT NULL COMMENT 'user name',
`password` varchar(64) NOT NULL,
`create_time` datetime,
`update_time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY `pk_id`(`id`),
UNIQUE KEY `uk_username`(`username`)
)
ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
- - 注意`COMMENT` 后使用的符号
- - 设置引擎
- - 设置默认字符串utf8
- - 查看数据库table的设置
```java
DESC accounting_test.accounting_userInfo
```
|Field|Type|Null| Key| Default|Extra|    
|:----:|:----:|:----:|:----:|:----:|:----:|
|id|bigint unsigned |NO |PRI |`<null>`|auto_increment|
username|     varchar(64)|      NO|    UNI|  `<null>`| 
|password     |varchar(64)      |NO||`<null>`|
|create_time  |datetime         |YES ||       `<null>`| 
update_time|  datetime|         YES||`<null>`|on update CURRENT_TIMESTAMP|
- - 更改数据库内容
```java
UPDATE accounting_userinfo SET password='111' where id=1
```
- 添加Mybatis 和Mysql 依赖
```java
<!-- mybatis-spring-boot-starter -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>

<!--Mysql driver-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.15</version>
</dependency>
```
- 添加Mapper,在Mapper里运行简单的数据库操作
```java
package com.Procarihana.AccountingService.Dao.Mapper;


import com.Procarihana.AccountingService.Moudle.presistence.UserInfo;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface UserInfoMapper {

    @Select("SELECT id, username, password, create_time, update_time FROM accounting_userinfo where id =#{id}")
    UserInfo getUserInfoByyUserId(@Param("id")Long id);

}
```
- 在Dao里完成数据库交互
```java
package com.Procarihana.AccountingService.Dao;

import com.Procarihana.AccountingService.Dao.Mapper.UserInfoMapper;
import com.Procarihana.AccountingService.Moudle.presistence.UserInfo;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

@Repository
public class UserInfoDaoImpl implements UserInfoDao{
    private final UserInfoMapper userInfoMapper;

    @Autowired
    public UserInfoDaoImpl(UserInfoMapper userInfoMapper) {
        this.userInfoMapper = userInfoMapper;
    }

    @Override
    public UserInfo getUserInfoById(Long id) {
        return userInfoMapper.getUserInfoByyUserId(id) ;
    }
}
```
## API接口
|Method| Description | Is Idempotent  |
|--|:--:|--:|
GET	|   `Return the current value of an object`	 |True|
PUT|	`Replace an object, or create a named object, when applicable`|	True
DELETE|	`Delete an object`|	True|
POST|	`Create a new object based on the data provided, or submit a command`	|False|
HEAD|	`Return metadata of an object for a GET response. Resources that support the GET method MAY support the HEAD method as well`|	True
PATCH|	`Apply a partial update to an object`|	False|
OPTIONS|	`Get information about a request; see below for details.`|	True

- 幂等（）
- - 一次请求和多次请求某个资源应该具有同样的副作用，并非相同结果
- - - `Get` 获取资源（不应有副作用），但结果不一定相同 
http://api.accountingService.com/resources/latest
- - - `POST` 创建资源（执行动作，有副作用） 多次提交创建出不同的资源（需要有防止多次频繁提交的操作）--->不是幂等
```
http://api.accountingService.com/resources/
http://api.accountingService.com/resources/1
http://api.accountingService.com/resources/2
```
- - - `PUT`更新操作，也是有副作用，满足幂等性
```
https://api.hardcore.com/resources/123
更新id为123的资源
```
- - 幂等性只与后端服务器的实际状态有关，而每一次请求接收到的状态码不一定相同。
- - - 例：`DELETE ` /idX/delete HTTP/1.1是幂等的，即便是不同请求之间接收到的状态码不一样：
```
DELETE /idX/delete HTTP/1.1   -> Returns 200 if idX exists
DELETE /idX/delete HTTP/1.1   -> Returns 404 as it just got deleted
DELETE /idX/delete HTTP/1.1   -> Returns 404
```
- - 类似：f(x)=f(f(x))                  

- Response格式
- - 基本要求
- - - JSON属性应该用camelCased
- - - Service应该将JSON作为默认编码
- - - Service必须要支持application/json，并且将application/json作为默认response format
```json
{
  "user": {
    "id": 1,
    "name": "hardcore"
  }
}
```
- - 错误请求的Response
- - - **一定要用HTTP status code**
- - - 最简单的三类：

- - - OK 200

- - - Client side error：4XX

- - - Service side error： 5XX
```json
{
  "error": {
    "code": "BadArgument",
    "message": "Multiple errors in ContactInfo data",
    "target": "ContactInfo",
    "details": [
      {
        "code": "NullValue",
        "target": "PhoneNumber",
        "message": "Phone number must not be null"
      },
      {
        "code": "NullValue",
        "target": "LastName",
        "message": "Last name must not be null"
      },
      {
        "code": "MalformedValue",
        "target": "Address",
        "message": "Address is not valid"
      }
    ]
  }
}
```

## 异常处理Exception
- 异常的报错给前端为Service部分，通过`ServiceException`来定义报错信息的内容
```java
import lombok.Data;

/**
 * Accounting Service Exception
 */
@Data
public class ServiceException extends RuntimeException {
    private int statusCode;
    private String errorCode;//biz error code
    private ServiceException.ErrorType errorType;//Service,Client,Unknown

    public enum ErrorType {
        Cline,
        Service,
        Unknown
    }

    public ServiceException(String message) {
        super(message);
    }
}

```

- 通过`Optional`给出异常信息
- - 先写出一个目标异常
```java

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

/**
 * Accounting Service ResourceNotFoundException
 */

@ResponseStatus(code = HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends ServiceException {
    public ResourceNotFoundException(String message) {
        super(message);
        this.setStatusCode(HttpStatus.NOT_FOUND.value());
    }
}
```
- - 通过`Optional`得出结果或者抛出异常
```java
 @Override
    public UserInfo getUserInfoByUserID(Long userId) {
        //com.Procarihana.AccountingService.Moudle.presistence.UserInfo userInfo = userInfoDao.getUserInfoById(userId);
        val userInfo = Optional.ofNullable(userInfoDao.getUserInfoById(userId))
                .orElseThrow(()->new ResourceNotFoundException(String.format(" User %s was not found",userId)));
        UserInfo commonUserInfo = userInfoPresToComConverter.convert(userInfo);
        return commonUserInfo;
    }
```

- 更改异常status --> 通过`@RseonseStatus`更改status
- - Eg：`500`-->`404`
```java
@ResponseStatus(code = HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends ServiceException {
    public ResourceNotFoundException(String message) {
        super(message);
        this.setStatusCode(HttpStatus.NOT_FOUND.value());

    }
}
```
```json
{
    "timestamp": "2020-08-12T02:53:49.581+0000",
    "status": 500,
    "error": "Internal Server Error",
    "message": " User 2 was not found",
    "path": "/v1/users/2"
}
```
===>
```json
{
    "timestamp": "2020-08-12T02:50:36.129+0000",
    "status": 404,
    "error": "Not Found",
    "message": " User 2 was not found",
    "path": "/v1/users/2"
}
```

- 自定义ErrorResponse
- - 先写一个异常错误信息类
```java
@Data
@Builder
public class ErrorResponse {
    private String code;
    private ServiceException.ErrorType errorType;
    private String massage;
    private int statusCode;
}
```
- - 放在需要自定义异常报错的地方,通过bulider模式完成异常报错的自定义。
``` java
public class UserController {
    private final UserInfoManager userInfoManager;
    private final UserInfoCToSeConverter userInfoCToSeConverter;

    @Autowired
    public UserController(final UserInfoManager userInfoManager,
                          final UserInfoCToSeConverter userInfoCToSeConverter
                          ) {
        this.userInfoManager = userInfoManager;
        this.userInfoCToSeConverter = userInfoCToSeConverter;

    }


    @GetMapping(value = "/{id}",produces = "application/json; charset=utf-8" )
    public ResponseEntity<?> getUserInfoByUserId(@PathVariable("id") Long userId) {
        log.info("Get user info by user id {}", userId);
        try {
            UserInfo userInfo = userInfoManager.getUserInfoByUserID(userId);
            return ResponseEntity.ok(userInfoCToSeConverter.convert(userInfo));
        } catch (ResourceNotFoundException ex) {
            return ResponseEntity.status(HttpStatus.NOT_FOUND)
                    .body(ErrorResponse.builder()
                            .code("USER_NOT_FOUND")
                            .errorType(ServiceException.ErrorType.Cline)
                            .massage(ex.getMessage())
                            .statusCode(HttpStatus.NOT_FOUND.value())
                            .build());
        }
    }

}
```
- - 异常信息
```json
{
    "code": "USER_NOT_FOUND",
    "errorType": "Cline",
    "massage": " User 2 was not found",
    "statusCode": 404
}
```

- - 通过统一处理`try/catch`来优化代码(放在一个类里)
- - - `@RestControllerAdvice`控制增强 
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ResourceNotFoundException.class)
    ResponseEntity<?> handleResourceNotFoundException(ResourceNotFoundException exception){
         ErrorResponse errorResponse = ErrorResponse.builder()
                 .statusCode(HttpStatus.NOT_FOUND.value())
                 .code("USER_NOT_FOUND")
                 .errorType(ServiceException.ErrorType.Cline)
                 .massage(exception.getMessage())
                 .build();
         return ResponseEntity.status(HttpStatus.NOT_FOUND)
                 .body(errorResponse);
    }
}
```
- - - 优化后
```java
@GetMapping(value = "/{id}", produces = "application/json; charset=utf-8")
    public ResponseEntity<?> getUserInfoByUserId(@PathVariable("id") Long userId) {
        log.info("Get user info by user id {}", userId);
        UserInfo userInfo = userInfoManager.getUserInfoByUserID(userId);
        return ResponseEntity.ok(userInfoCToSeConverter.convert(userInfo)); //为空的时候会抛出异常，所以不会为空
    }
```
- 设置一个通用的Exception来处理异常
- - `setErrorCode`一般很少会直接定义，会根据不同的需要在return处来决定，除非是简单且非常明确的异常
```java
public class InvalidParameterException extends ServiceException{
    public InvalidParameterException(String message){
        super(message);
        this.setStatusCode(HttpStatus.BAD_REQUEST.value()); //BAD_REQUEST.value()为400.
        this.setErrorCode("INVALID_PARAMETER");
        this.setErrorType(ErrorType.Cline);
    }
}
```
- - 使用
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(InvalidParameterException.class)
    ResponseEntity<?> handleInvalidParameterException(InvalidParameterException exception) {
        ErrorResponse errorResponse = ErrorResponse.builder()
                .statusCode(exception.getStatusCode())
                .code("USER_NOT_FOUND")
                .errorType(exception.getErrorType())
                .massage(exception.getMessage())
                .build();
        return ResponseEntity.status(exception.getStatusCode() != 0 ?
                exception.getStatusCode()
                :HttpStatus.INTERNAL_SERVER_ERROR.value()) //如果为空就返回500
                .body(errorResponse);
    }
}
```
- 当Exception 继承某个类的时候，通过↓，可以根据限制条件来自动选择执行Exctption
- - Eg：Exception 都继承ServiceException
```java
public class InvalidParameterException extends ServiceException{
}
public class ResourceNotFoundException extends ServiceException {
}
```
- - 根据使用的条件
```java
@GetMapping(value = "/{id}", produces = "application/json; charset=utf-8")
    public ResponseEntity<?> getUserInfoByUserId(@PathVariable("id") Long userId) {
        log.info("Get user info by user id {}", userId);
        if (userId == null |userId <=0){
         throw new InvalidParameterException(String.format("The user id %s is invalid.",userId));
        }
        UserInfo userInfo = userInfoManager.getUserInfoByUserID(userId);
        return ResponseEntity.ok(userInfoCToSeConverter.convert(userInfo)); //为空的时候会抛出异常，所以不会为空
    }
}
```
- - 直接执行对应的exception，从而能够全局地处理异常
```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(ServiceException.class)
    ResponseEntity<?> handleParameterException(ServiceException exception) {
        ErrorResponse errorResponse = ErrorResponse.builder()
                .statusCode(exception.getStatusCode())
                .code("USER_NOT_FOUND")
                .errorType(exception.getErrorType())
                .massage(exception.getMessage())
                .build();
        return ResponseEntity.status(exception.getStatusCode() != 0 ?
                exception.getStatusCode()
                : HttpStatus.INTERNAL_SERVER_ERROR.value()) //如果为空就返回500
                .body(errorResponse);
    }
}

```
## 单元测试
- SpringBoot tset maven
- - exclusion 是为了把Junit4排除，从而使用Junit5 
```java
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
```
- package :utils
- - 用于继承需要测试的接口，然后build一个实例
- - - 接口
```java
@Mapper
public interface UserInfoMapper {

    @Select("SELECT id, username, password, create_time, update_time FROM accounting_userinfo where id =#{id}")
    UserInfo getUserInfoByyUserId(@Param("id")Long id);

}
```
- - - 实现
```java
public class UserInfoMapperTestLmp implements UserInfoMapper {

    @Override
    public UserInfo getUserInfoByyUserId(Long id) {
        return id >0?UserInfo.builder()
                .id(id)
                .username("TestUsername")
                .password("TestPassword")
                .updateTime(LocalDate.now())
                .createTime(LocalDate.now())
                .build():null;
    }
}
```
- `@BeforeEach`
- `assertEquals`
- `assertThrow`
```java
/**
	 * <em>Assert</em> that execution of the supplied {@code executable} throws
	 * an exception of the {@code expectedType} and returns the exception.
	 *
	 * <p>If no exception is thrown, or if an exception of a different type is
	 * thrown, this method will fail.
	 *
	 * <p>If you do not want to perform additional checks on the exception instance,
	 * simply ignore the return value.
	 */
	public static <T extends Throwable> T assertThrows(Class<T> expectedType, Executable executable) {
		return AssertThrows.assertThrows(expectedType, executable);
	}
```
```java
  @Test
    public void testUserInfoByInvalidUserId() {
        Long userId = -1L;
        Assertions.assertThrows(ResourceNotFoundException.class, () -> userInfoManager.getUserInfoByUserID(userId));
    }
```
- `assertThat` AssertJ
- - 引入的包要正确`import static org.assertj.core.api.Assertions.assertThat;`

```java
    @Test
    public void testUserInfoByUserId() {
        Long userId = 1L;

        UserInfo result = userInfoManager.getUserInfoByUserID(userId);
        //AssertJ
        assertThat(result).isNotNull()
                .hasFieldOrPropertyWithValue("id",userId)
                 //有没有这个属性,并核对属性的内容是否正确
                .hasFieldOrPropertyWithValue("username","TestUsername")
                .hasFieldOrPropertyWithValue("password","TestPassword");

    }
```
- 和没有Mockito对比,需要去创建一个真实的对象
```java
public class UserInfoManagerTest {
    private UserInfoManager userInfoManager;

    @BeforeEach
    public void setup() {
        val userInfoMapper = new UserInfoMapperTestImp();
        val userInfoPresToComConverter = new UserInfoPresToComConverter();
        val userInfoDao = new UserInfoDaoImpl(userInfoMapper);
        userInfoManager = new UserInfoManagerImpl(userInfoDao, userInfoPresToComConverter);
    }

    @Test
    public void testUserInfoByUserId() {
        Long userId = 1L;

        UserInfo result = userInfoManager.getUserInfoByUserID(userId);

        Assertions.assertEquals("TestUsername", result.getUsername());
        Assertions.assertEquals("TestPassword", result.getPassword());

    }

```

- Mockito
- - `verify` == `assert`，都是用于判断
- - `when/then` 用于设定一个模拟对象进行测试
- - `do` == `when/then`  
> doNothing  
> doReturn  
> doThrow  
> - 注意使用的方法  

```java
 doThrow(exception).when(mock).someVoidMethod();
```
sort？？？
- - 区别在于Mockito在调用`spy()`进行实现的过程中，执行`when/then` 的时候，如果有规定模拟的结果就会返回该模拟结果，如果没有，则会返回真是数据的结果。而执行`do`方法则会和普通的调用`mock()`一样只会调用提前设置好的虚拟对象和结果，不会返回真实的数据
- - `spy()`如果一个方法定制了返回值或者异常，那么就会按照定制的方式被调用执行；如果一个方法没被定制，那么调用的就是真实类的方法。
cnblogs.com/softidea/p/4204389.html

- `MockitoAnnotations.initMocks(this);`  用来体型Mockito 识别对象
 > MockitoAnnotations.initMocks(this); initializes fields annotated with Mockito annotations.
 See also {@link MockitoSession} which not only initializes mocks
 but also adds extra validation for cleaner tests!

- ControllerTest
- - MockMvc 虚拟出一个Mvc用于测试
```java
@ExtendWith(MockitoExtension.class)
@ExtendWith(SpringExtension.class)
public class UserControllerTest {
 private MockMvc mockMvc;
}
 ```
- - 运行Controller 需要用到的业务通过Mock虚拟出来,如果是简单的转换等的方法可以通过new 出新的对象来完成
```java
 @Mock
 public class UserControllerTest {
    private UserInfoManager userInfoManager;
 }
```
- - 装配mockMvc
```java
 @BeforeEach
    void setup() {
        mockMvc = MockMvcBuilders.standaloneSetup(
                new UserController(
                        userInfoManager, new UserInfoCToSeConverter()))
                .setControllerAdvice(new GlobalExceptionHandler()) //通过`setControllerAdvice`引入自定义的异常
                .build();
    }
``` 
- - Mock出来的虚拟对象如果要进行多次的测试，且每次测试对象的内容都不一样的时候，要进行清空
```java
   @AfterEach
    void  teatDown(){
        reset(userInfoManager);
    }
```
- - 检测返回的json结果是否正确
```java
    .andExpect(content().string(new ObjectMapper().writeValueAsString(userInfoS)))
    .andExpect(content().string("{\"id\":2,\"username\":\"666\",\"password\":null}"));
```
- - 进行测试的时候
- - - 要注意`@Test`引入的包是否正确
- - - `doReturn(返回对象).when(条件对象).条件对象的方法`格式
- - - `mockMvc.perform(get(""))`要以"/"开头
- - - 最后要用`verify`来检测`@Mock`的对象是否正常运行
```java
  @Test
    void getUserInfoByUserIdTest() throws Exception {
        String username = "666";
        String password = "666";
        LocalDate createTime = LocalDate.now();
        LocalDate updateTime = LocalDate.now();
        Long userId = 2L;
        UserInfo userInfoC = UserInfo.builder()
                .id(userId)
                .username(username)
                .password(password)
                .build();
        doReturn(userInfoC).when(userInfoManager).getUserInfoByUserID(anyLong());


        mockMvc.perform(get("/v1.0/users/" + userId))
                .andExpect(status().isOk())
                .andExpect(content().contentType("application/json;charset=utf-8 "))
                .andExpect(content().string("{\"id\":2,\"username\":\"666\",\"password\":null}"));
        verify(userInfoManager，never).getUserInfoByUserID(anyLong());
    }

```

## CheckStyle
- maven 
- - `<phase>validate`是在没有编译代码的时候进行检查，如果是verify则是在最后的阶段进行检查
- - 引用后要记得更改configLocation`<configLocation>checkstyle.xml</configLocation>`--> 
```java
<plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-checkstyle-plugin</artifactId>
        <version>3.1.1</version>
        <configuration>
          <configLocation>${project.basedir}/src/main/resources/checkstyle_checks.xml</configLocation>
          <encoding>UTF-8</encoding>
     <consoleOutput>true</consoleOutput>
     <failsOnError>true</failsOnError>
     <linkXRef>false</linkXRef>
        </configuration>
        <executions>
     <execution>
       <id>validate</id>
       <phase>validate</phase>
       <goals>
         <goal>check</goal>
       </goals>
     </execution>
   </executions>
      </plugin>
```
- `checstyle_checks.xml` 一般会放在resources 里
- -  `<property name="severity" value="error"/>`
> 运行的时候CheckStyle出现问题就会直接失败,而原始状态的`warning`则只是警告
- - `<property name="basicOffset" value="4"/>`
>缩进 大多数的库缩进都是4个空格，最好checkstyle 的时候改成4
- - import
> - `AvoidStarImport` 禁止包名以* 的形式省略
> - `UnusedImports` 不能存在没有使用的Imports
> -  ` <module name="CustomImportOrder">`用于管理imports顺序  
顺序：先Static，然后是special的
```java
<property name="customImportOrderRules"
                      value="STATIC###SPECIAL_IMPORTS###THIRD_PARTY_PACKAGE###STANDARD_JAVA_PACKAGE"/>
```
```java
 <module name="AvoidStarImport"/>
        <module name="RedundantImport"/>
        <module name="UnusedImports"/>
        <module name="CustomImportOrder">
            <property name="specialImportsRegExp" value="(com.Procarihana)"/>
            <property name="standardPackageRegExp" value="^(java|javax)\."/>
            <!--<property name="thirdPartyPackageRegExp" value="^(com|org|lombok)\."/>-->
            <property name="sortImportsInGroupAlphabetically" value="true"/>
            <property name="separateLineBetweenGroups" value="true"/>
            <property name="customImportOrderRules"
                      value="STATIC###SPECIAL_IMPORTS###THIRD_PARTY_PACKAGE###STANDARD_JAVA_PACKAGE"/>
```
- 运行：mvn checkstyle:check
- target里的result可以看到错误的记录
- - `CheckStyle-Idea` plugin安装，方便修改错误  
https://www.jianshu.com/p/6b51b8ceefa5
> - 注意checkStyle-ide 和 maven checkStyle（在version点进去后可以看到） 版本一置
> - 更改code style 时要注意查看一下有没有问题

## SportBug
- pom.xml
https://www.jacoco.org/jacoco/trunk/doc/examples/build/pom.xml
- 期望代码检查的覆盖率为0.6
- - 测试后可以通过`target -> site -> jacoco -> index.xml`里面找到覆盖情况 
```java
<limit>
                                            <counter>COMPLEXITY</counter>
                                            <value>COVEREDRATIO</value>
                                            <minimum>0.60</minimum>
                                        </limit>
```
```java
<plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>0.8.6-SNAPSHOT</version>
                <executions>
                    <execution>
                        <id>default-prepare-agent</id>
                        <goals>
                            <goal>prepare-agent</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>default-report</id>
                        <goals>
                            <goal>report</goal>
                        </goals>
                    </execution>
                    <execution>
                        <id>default-check</id>
                        <goals>
                            <goal>check</goal>
                        </goals>
                        <configuration>
                            <rules>
                                <rule>
                                    <element>BUNDLE</element>
                                    <limits>
                                        <limit>
                                            <counter>COMPLEXITY</counter>
                                            <value>COVEREDRATIO</value>
                                            <minimum>0.60</minimum>
                                        </limit>
                                    </limits>
                                </rule>
                            </rules>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
```
- 当使用lombok自动配置对象的builder的时候，jacoco是辨别不出来此为储存内容的对象，使得测试率下降。
https://medium.com/@mladen.bolic/lombok-data-improve-your-code-coverage-a74fb624a72b
- - `lombok.config`要放在根目录！（和pom.xml）一样的位置！
```java
# lombok.config
lombok.addLombokGeneratedAnnotation = true
```

## 在GitHub Actions中创建一个持续集成（CI）工作流，以使用Maven构建和测试Java项目
https://docs.github.com/en/actions/language-and-framework-guides/building-and-testing-java-with-maven
- 新建一个目录`.github/workflows`
- 在里面新建一个`maven.xml`文件（文件名可以任意取，但是后缀名统一为.yml）
- 复制
```java
  
# This workflow will build a Java project with Maven
# For more information see: https://help.github.com/actions/language-and-framework-guides/building-and-testing-java-with-maven

name: Java CI with Maven

on:
  push:
    branches: [ $default-branch ]
  pull_request:
    branches: [ $default-branch ]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
- - 此工作流程执行以下步骤：

1. 该checkout步骤将在运行程序上下载存储库的副本。
2. 该setup-java步骤配置Java 1.8 JDK。
3. “使用Maven进行构建”步骤package以非交互方式运行Maven 目标，以确保您的代码得以构建，通过测试并可以创建包。
- - branches 一般都是master 或者是自己其他的branch(branch 名字写不对是识别不了Ci的，不要问为什么知道……)
- - maven run 的内容根据需要 ，一般都是 `mvn verify`做最后的检查 

- spotbug 设置
- - maven
## Controller 
- Mapping
- - 路径里面添加变量
```java
@RestController
public class HelloController {

    @GetMapping("greeting/{name}")
    public String sayHello(@PathVariable("name")String name){
        return String.format("Hello,%s",name);

    }

}
http://127.0.0.1:8080/greeting/aa
效果:Hello,aa
```
```java
    @GetMapping("greeting/")
    public String sayHello(@RequestParam("name")String name){
        return String.format("Hello,%s",name);

    }
http://127.0.0.1:8080/greeting?name=aa    
效果:Hello,aa
```
- 通过Mapping里面表明来说明版本问题
```
@RequestMapping("v1/users")
public class UserController{

}
```
- Controller 使用的是Service 对象，因为是直接给前端的 

## 使用Swagger 创建API
- 自动生成API文档，专门为SpringFamily
- maven
- - `springfox-swagger2` 只是文档，没有可读性。Swagger UI:提供了一个可视化的UI页面展示描述文件。
```java
 <!--Swagger-UI API doc generator-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.7.0</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.7.0</version>
        </dependency>
```
- -  配置 SpringFoxConfig
- - - 注意的是configuration 和 swagger
```java
@Configuration
@EnableSwagger2
public class SpringFoxConfig {
    /**
     * The bean for Docket.
     *
     * @return Docket bean instance.
     */
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
            .select()
            .apis(RequestHandlerSelectors.any())
            .paths(PathSelectors.any())
            .build();
    }

}
```
- - 配置 ShiroConfig
> - swagger-ui、 swagger-ui的资源无论如何都要可以访问的
> - v2 web架构？

- - 改善各个Controller 的注解
> - `@Api`  value:对整个Controller进行描述
> - `@ApiOperation` 对Controller 下的 接口进行描述（get、post、put……）`value`:写API描述，`resource`:写返回的类型
> - `@ApiResponses`允许放status、message等，表现每个status表示什么问题
>- 只需要在controller 上面完成就可以了
## shiro
- 是一个强大灵活的开源安全框架，可以完全处理身份验证、授权、加密和会话管理。
> Authentication（认证）, Authorization（授权）, Session Management（会话管理）, Cryptography（加密）被 Shiro 框架的开发团队称之为应用安全的四大基石。
> - Authentication（认证）：用户身份识别，通常被称为用户“登录”
> - Authorization（授权）：访问控制。比如某个用户是否具有某个操作的使用权限。
> - Session Management（会话管理）：特定于用户的会话管理,甚至在非web 或 EJB 应用程序。
> - Cryptography（加密）：在对数据源使用加密算法加密的同时，保证易于使用。
- 在概念层，Shiro 架构包含三个主要的理念：Subject,SecurityManager和 Realm。
> - Subject：当前用户，Subject 可以是一个人，但也可以是第三方服务、守护进程帐户、时钟守护任务或者其它–当前和软件交互的任何事件。
> - SecurityManager：管理所有Subject，SecurityManager 是 Shiro 架构的核心，配合内部安全组件共同组成安全伞。
> - Realms：用于进行权限信息的验证，我们自己实现。Realm 本质上是一个特定的安全 DAO：它封装与数据源连接的细节，得到Shiro 所需的相关的数据。在配置 Shiro 的时候，你必须指定至少一个Realm 来实现认证（authentication）和/或授权（authorization）。
https://zhuanlan.zhihu.com/p/54176956
- Shiro 实现基本登录
- - maven
```java
<!-- https://mvnrepository.com/artifact/org.apache.shiro/shiro-spring -->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.5.3</version>
</dependency>
```
- - ShiroConfig 
> - 引入的`SecurityManager`的包一定是`import org.apache.shiro.mgt.SecurityManager;`
> - `SecurityManager`需要一个实现Realm（范围）接口的实例作为参数来用于获得AuthenticationInfo（用户信息）
> - `ShiroFilter`拦截器
> > -Filter
> > - - 判断请求是否合法，根据条件完成请求的处理，所有线程都结束工作或者timeout的话就执行`DESTORY`
> > - - Filter Chain Eg:`request -> f1 -> f2 -> handler -> f3 ->response`
> > - - defalult Filture 可以看到权限的枚举
 ```java
package javax.servlet;    
public default void init(FilterConfig filterConfig) throws ServletException {}
    public default void init(FilterConfig filterConfig) throws ServletException {}
    public default void destroy() {}
    }
```java

@Configuration
public class ShiroConfig {
    @Bean
    public SecurityManager securityManager(Realm realm) {
        return new DefaultWebSecurityManager(realm);
    }
 
    /**
     * Shiro Filter,实现权限相关拦截
     * <p>
     * anon: Not need login access
     * authc: required login ,and then access
     * user: remember me -> access
     * role: role -> access
     */

    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager) {
        val shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager);

        //控制url用什么权限访问，常用LinkedHashMap
        val shiroFilterDefinitionMap = new LinkedHashMap<String, String>();
        //map需要顺序，因为有'/**'的设置，如果没有顺序，容易造成`/**`控制所有的页面，其他的权限控制无效的情况

        shiroFilterDefinitionMap.put("/v1.0/greeting", "authc"); //需要登录才能访问
        shiroFilterDefinitionMap.put("v1.0/users/", "anon"); //不需要登录就能访问
        shiroFilterDefinitionMap.put("/**", "authc");//其他的页面需要登录才能够访问

        shiroFilterFactoryBean.setFilterChainDefinitionMap(shiroFilterDefinitionMap);
        return shiroFilterFactoryBean;
    }

}
```
- - Realm 实现 userRealm为例
> - `@Component`使得`SecurityManager`能够识别
> - password 和 username 比较好的异常
```java
  
            throw new UnknownAccountException(String.format("The username %s was not found.",username));

 
            throw new IncorrectCredentialsException("The password is invalid for username %s.")
    
```
> - 完成User 的认证
```java
        return new SimpleAuthenticationInfo(UserInfo.getUsername, password, this.getName()); //认证
```
···
- - SessionController
## 密码加密
- 密码不能明文传输，一定要加密
- 数据库要储存加密后的密码
- - 基本加密：MD5 SHA5 `EG：MD5(password) -> newPassword -> database`
- - 加盐加密 : `EG: MD5(password + salt) -> newPassword -> database`
- - 多次迭代加盐加密
```java
Round 1 
MD5(password + salt) -> newPassword_1 
Round 2
MD5(newPassword_1 + salt) -> newPassword_2
……
MD5(newPassword_n-1 + salt) -> newPassword_n -> database
```
- 数据库加密版数据库……
```js
create table `accounting_userinfo`(
`id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
`username` varchar(64) NOT NULL COMMENT 'user name',
`password` varchar(64) NOT NULL,
`salt` varchar(50) NOT NULL,
`create_time` datetime,
`update_time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
PRIMARY KEY `pk_id`(`id`),
UNIQUE KEY `uk_username`(`username`)
)
ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
- 在manager 处加盐
```java
 @Override
    public UserInfo register(String username, String password) {
        val userInfo  = userInfoDao.getUserInfoByUsername(username);
        if (userInfo != null){
            throw new InvalidParameterException(String.format("The user %s was registered."));
        }

        // Set random salt
        String salt = UUID.randomUUID().toString();
        String encryptedPassword = new Sha256Hash(password,salt, HASH_ITERATIONS).toBase64();
        val newUserInfo = com.procarihana.accounting.moudle.presistence.UserInfo.builder()
            .username(username)
            .password(encryptedPassword)
            .salt(salt)
            .createTime(LocalDateTime.now())
            .updateTime(LocalDateTime.now())
            .build();
        userInfoDao.createNewUser(newUserInfo);
        return userInfoPresToComConverter.convert(newUserInfo);

    }
```
- 用户认证的时候需要带上`salt`来进行密码的认证，所以要在`UserReaml`处认证的时候后也要带上`salt`
- - 在`AuthenticationInfo`进行认证的时候后，需要`salt`的参数为BySource 。String -> ByteSouce ==> ByteSource.Uyil.bytes(stirng)完成,要注意引用的类。
```java
import org.apache.shiro.util.ByteSource;

 @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        String username = (String) token.getPrincipal(); //主项
        String password = new String((char[]) token.getCredentials()); //凭证
        val userInfo = userInfoManager.getUserInfoByUsername(username);
        val salt = ByteSource.Util.bytes(userInfo.getSalt());
        return new SimpleAuthenticationInfo(userInfo.getUsername(), userInfo.getPassword(), salt, this.getName()); //认证
    }
```

## Tag 
- table
```java
create table `accounting_tag`(
   `id`          bigint(20) unsigned NOT NULL AUTO_INCREMENT,
    `description` varchar(50)         NOT NULL,
    `user_id`     bigint(20) unsigned NOT NULL,
    `status`      tinyint(1) unsigned NOT NULL COMMENT '0-> disabled, 1-> enabled',
    `create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
    `update_time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY `pk_id` (`id`),
    KEY `idx_uid` (`user_id`),
    FOREIGN KEY (`user_id`) REFERENCES hcas_userinfo (`id`)
)
ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
- - foreigen key 是其他表属性的映射
- - status用0和1表示的话用`tinyint（1)`

## Record
- table
```java
create table `accounting_record`(
`id` bigint(20) unsigned NOT NULL AUTO_INCREMENT, 
`amount` decimal(18,2) NOT NULL DEFAULT'0.00', 
`note` varchar(200) DEFAULT NULL, 
`category` tinyint(1) unsigned NOT NULL COMMENT'0-> outcome, 1-> income', 
`status` tinyint(1) unsigned NOT NULL COMMENT'0-> disable, 1-> enable', 
`create_time` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP, 
`update_time` datetime DEFAULT NULL ON UPDATE CURRENT_TIMESTAMP, 
`user_id` bigint(20) unsigned NOT NULL,
PRIMARY KEY `pk_id`(`id`), 
KEY `user_key`(`user_id`), 
FOREIGN KEY (`user_id`) REFERENCES accounting_userinfo(`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8; 
```


## DAO
- `@Repository`来表示数据库，用于封装存储的
```java
import org.springframework.stereotype.Repository;

@Repository
public class UserInfoDaoImpl implements UserInfoDao{
    @Override
    public UserInfo getUserInfoById(Long id) {
        return null;
    }
}

```
- Dao 使用的是prisistence对象


## Manager 
- manager部分使用的是common的对象
- manager 需要向数据库转换，也需要向Controller转换，可以直接通过converter完成


## Converter
- 通过继承`Converter`方法来完成类型的转换 (Guava maven)
Eg
```java
package com.Procarihana.AccountingService.converter.presisitenceToCommon;

import com.Procarihana.AccountingService.Moudle.presistence.UserInfo;
import com.google.common.base.Converter;

import java.time.LocalDate;

public class UserInfoConverter extends Converter<UserInfo, com.Procarihana.AccountingService.Moudle.common.UserInfo> {
    @Override
    protected com.Procarihana.AccountingService.Moudle.common.UserInfo doForward(UserInfo userInfo) {
        return com.Procarihana.AccountingService.Moudle.common.UserInfo.builder()
                .username(userInfo.getUsername())
                .password(userInfo.getPassword())
                .id(userInfo.getId())
                .build();
    }

    @Override
    protected UserInfo doBackward(com.Procarihana.AccountingService.Moudle.common.UserInfo userInfo) {
        return UserInfo.builder()
                .createTime(LocalDate.now())
                .updateTime(LocalDate.now())
                .id(userInfo.getId())
                .password(userInfo.getPassword())
                .username(userInfo.getUsername())
                .build();
    }

}

```
```java
package com.Procarihana.AccountingService.Manager;

import com.Procarihana.AccountingService.Dao.UserInfoDao;
import com.Procarihana.AccountingService.Moudle.common.UserInfo;

import com.Procarihana.AccountingService.converter.presisitenceToCommon.UserInfoConverter;
import org.springframework.stereotype.Component;

@Component
public class UserInfoManagerImpl implements UserInfoManager {
    private final UserInfoDao userInfoDao;
    private final UserInfoConverter userInfoConverter;
    @Autowired
    public UserInfoManagerImpl(final UserInfoDao userInfoDao,
                               final UserInfoConverter userInfoConverter) {
        this.userInfoDao = userInfoDao;
        this.userInfoConverter = userInfoConverter;
    }

    @Override
    public UserInfo getUserInfoByUserID(Long userId) {
        com.Procarihana.AccountingService.Moudle.presistence.UserInfo userInfo = userInfoDao.getUserInfoById(userId);
        UserInfo commonUserInfo = userInfoConverter.convert(userInfo);  
        return userInfoConverter.convert(userInfo);


    }
}
```
```java
//翻转
    com.Procarihana.AccountingService.Moudle.presistence.UserInfo userInfo1 = userInfoConverter.reverse().convert(commonUserInfo); 
```

- - doBaceWard 的时候，需要根据实际情况选择是否能够转换，转换的内容为空的时候应该如何处理等
- 转换不能调用Service、io等操作
## Mark Little Point
1. `%s`--> 字符串类型
2. LocalDatetime vs Instant
- LocalDateTime对于用户来说，可能就只是一个简单的日期和时间的概念，考虑如下的例子：两个人都在2013年7月2日11点出生，第一个人是在英国出生，而第二个是在加尼福利亚，如果我们问他们是在什么时候出生的话，则他们看上去都是 在同样的时间出生（就是LocalDateTime所表达的），
但如果我们根据时间线（如格林威治时间线）去仔细考察，则会发现在出生的人会比在英国出生的人稍微晚几个小时（这就是Instant所表达的概念，并且要将其转换为UTC格式的时间）。
3. 添加`projectlombok maven`加上注解能够自动进行对象的getter和setter,builder模式等等
```java
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.12</version>
            <scope>provided</scope>
        </dependency>

```
```java
package com.Procarihana.AccountingService.Moudle.presistence;
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class UserInformation {
    private Long id;
    private String username;
    private String password;
    private LocalDate createTime;
    private LocalDate  updateTime;
}
```
- `val` 能够自动识别对象的类型 (需要在IDE下载Lombok的plugin，并且把设置都选上才能正常使用)
```java
        com.Procarihana.AccountingService.Moudle.common.UserInfo userInfo = userInfoManager.getUserInfoByUserID(userId);
        ==>
        val userInfo = userInfoManager.getUserInfoByUserID(userId);

val userInfo ==>  com.Procarihana.AccountingService.Moudle.common.UserInfo userInfo = userInfoManager.getUserInfoByUserID(userId);
 userInfo
```
- `@Slf4j` 
```
import lombok.extern.slf4j.Slf4j;

@Slf4j
@RestController
public class UserController {
log.info("Get user info by user id {}",userId);
}
```
- `@NoArgsConstructor``@AllArgsConstructor`设置构造器无参数或者全参数，

4. `@RestController`  Vs `@Controller`
- @RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。
RestController使用的效果是将方法返回的对象直接在浏览器上展示成json格式，而如果单单使用@Controller会报错，需要ResponseBody配合使用。
- 如果只是使用@RestController注解Controller类，则方法无法返回jsp页面，配置的视图解析器InternalResourceViewResolver不起作用，返回的内容就是Return 里的内容。
例如：本来应该到success.jsp页面的，则其显示success.
- 如果需要返回到指定页面，则需要用 @Controller配合视图解析器InternalResourceViewResolver才行。
- 如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。
```java
@Controller  
@ResponseBody  
public class MyController {

 }  

或者在方法上加@ResponseBody  
@Controller  
public class MyController {

    @ResponseBody
    @RequestMapping(value = "/sutdent", method = RequestMethod.GET)
    public Student uploadFile() {
    
    }
 }  
  
@RestController  
public class MyRestController { }  
```
链接：https://www.jianshu.com/p/

5. Guava
- Converter 
6. 解决同名不同包的区分
   通过`@Qualifier`注解来提醒？？？？？？？？？？？？？
```java
import org.springframework.beans.factory.annotation.Qualifier;

public class UserInfoManagerImpl implements UserInfoManager {
    private final UserInfoDao userInfoDao;

    @Autowired
    public UserInfoManagerImpl(@Qualifier("UserInfoMysqlDao") UserInfoDao userInfoDao) {
        this.userInfoDao = userInfoDao;
    }

```

6. 整个项目的框架是SpringBoot框架搭建的，而Mapper这个类是用Mybatis-SpringBoot-start构建起来的，所以`@Autowired`不一定能够识别出来，mapper实例可能会出现没有Bean的情况，虽然这种情况下可以编译动过，运行也没有问题，但是还是会报错。
```java
@Repository
public class UserInfoDaoImpl implements UserInfoDao{
    private final UserInfoMapper userInfoMapper;

    @Autowired
    public UserInfoDaoImpl(UserInfoMapper userInfoMapper) {
        this.userInfoMapper = userInfoMapper;
    }
}
```
==>
```java
import lombok.RequiredArgsConstructor;

@Repository
@RequiredArgsConstructor(onConstructor = @__(@Autowired))
public class UserInfoDaoImpl implements UserInfoDao{
    private final UserInfoMapper userInfoMapper;
}
```
7. `Optional`
8. `enum`
9. `@ExtendWith `is a repeatable annotation that is used to register extensions for the annotated test class or test method.
- 是一个可重复的注释，用于为已注释的测试类或测试方法注册扩展。
- - 在Mockito 添加后就可以不用添加`MockitoAnnotionals `, 可以自动生成一个测试对象，不需要额外new出来
```java
class UserInfoDaoTest {
    @Mock
    private UserInfoMapper userInfoMapper;
    private UserInfoDao userInfoDao;

   @BeforeEach
   void setup(){
       MockitoAnnotations.initMocks(this);
       userInfoDao = new UserInfoDaoImpl(userInfoMapper);
   }
```
==>
```java
@ExtendWith(MockitoExtension.class)
class UserInfoDaoTest {
    @Mock
    private UserInfoMapper userInfoMapper;
    @InjectMocks
    private UserInfoDaoImpl userInfoDao;
```
10.  @标签应有非空说明。--> `<b>must not</b> be null`
```java
 * @param userInfoCToSeConverter ; <b>must not</b> be null
```
11. SpringIoc（控制翻转）和DI（依赖注入）
- https://www.cnblogs.com/xdp-gacl/p/4249939.html


12. SQL 和 NoSQL 
- 是否需要支持Transaction：NOSQL Transaction支持有限，实现麻烦
- 是否会有丰富的QueryBuilder
- 对TPS 要求是否高？是否需要很好的可拓展性
- 花式查询（Query Builder）：根据多种条件进行同时的查询，SQL就能够很好地支持（Eg：根据多个tag查询数据）
- TBS:可拓展性 Nosql比Mysql要强
- - Mysql ：通过读和写请求分开处理的负载分库分库分表查询的时候后就难以查询通过负载均衡的请求，使得TBS比较低
- - Nosql 对强一致性不友好，update后不一定能马上改，而修改的时间也不可预测
## 业务框架
- 服务的边界，核心。罗列出所有的功能，然后选出核心功能MVP。
- - UserManager、RecordManager、TagManager
- - 微服务架构会通过Service来构建逻辑，而组建则会通过Manager完成
- 完成记账业务
1. 只用一个record表完成记账。用户删除数据的时候会改变数据的status，并不会真正地删除，会出现用户检索到已经删除的数据但是不能查看的情况。每一次更新都需要把tags全部更新，检索顺序可能会出现问题。
2. 用record 和tags 完成记录。
- - record 包含 recordid、userid、支出收入（amount）、备注等
- - tags 包含tagid、userid、recordid、tag描述
- - record 会对应多个tag，就会出现一个record被多个tag多次储存的情况，导致数据冗余
3. 使用三个表完成（通过中间表完成）。record、record-tag-mapping、tags
- - record 和tags 分别储存各自的信息，在map里面储存record对应的各个tag。
- 测试
- - 单元测试
- - 集成测试
- - 性能测试
> - 在controller 里 把传入的数据都要过滤，减少非法数据进入数据库查询
```java
 @PutMapping(path = "/{id}", consumes = "application/json", produces = "application/json")
    public Tag updateTag(@PathVariable("id") Long tagId, @RequestBody Tag tag) {
        String status = tag.getStatus();
        if (status != null && "ENABLE".equals(status) && "DISABLE".equals(status)) {
            throw new InvalidParameterException(String.format("The status [%s] to update is invalid status", status));
        }
        if (tag.getUserId() ==null || tag.getUserId() <= 0L){
            throw new InvalidParameterException("The user id is empty or invalid");
        }
        if (tagId ==null || tagId <= 0L){
            throw new InvalidParameterException("The tag id is empty or invalid");
        }
        userInfoManager.getUserInfoByUserID(tag.getUserId());
        tag.setId(tagId);

        val tagInC = tagC2SConverter.reverse().convert(tag);
        val newTag = tagManager.updateTag(tagInC);
        return tagC2SConverter.convert(newTag);
    }
}
```
- - BUD测试 
- 检测报警
- - 系统性能监控
- - error、latency
- - 业务层面监控
- 部署与发布
- - 金丝雀发布。逐个服务进行发布，一步步测试是能够成功
#### 记账服务模块
- Record
- - 记录一笔账单交易 creatNewRecordByRecordId
- - 编辑单条账单记录 updateRecordByRecordId
- - 删除单挑账单记录 deleteRecordByRecordId
- - 查询单挑账单记录 getRecordByRecordlId
- - 按条件查询账单记录（会不会涉及到分页）
- Tag
- - 列出用户所有标签 listAllTagsByUserId
- - 新增标签 createNewTagForUserId
- - 编辑已有标签 updateTagBytagId
- - 删除已有标签 deleteTagByTagId

- 用户用例图 ->UML
- - 用例：用户能够使用的服务，扩展用例则是实现这些服务的手段，或者是相关的子服务，主要看是什么关系（include、extend）
- 分页和流水分页
- - pageSize 
- - pageNumber 
- - cursor -> 游标：从第几页开始查看
- 贫血模型和充血模型
- - 充血模型会把相应的接口方法直接放在moudel里，但是一般都会用贫血模型，相应的业务会分开处理和存放
```java
//贫血模型
public calss UserInfo(){
    private Long userId;
    private String username;
    private String password;
}

public calss UserInfo(){
    private Long userId;
    private String username;
    private String password;
    
    void updateUserInfoByUserId()；

    UserInfo getuserInfoByUserId(); 
}
```

- `@Request`改善数据库和实例moudle的变量名不同一或者嵌套等情况造成的映射失败、不完全映射的情况
- - `@One``@Many`一对以一或者一对多。Eg：一个文章里面有多个Tag,就需要`@Many`
- converter 转换的时候，status要注意，不能够直接转换，如果status是空的话，就会全都是0，就会全都变成disable
- `@Provider` -> 能够提供SQL语句的注解，有`@UpdataProvider`和`@SelectProvider`、`@InsertProvider`,不需要直接写出SQL语句。把SQL语句写到class Provider里面，通过`type = provider.class`来引用，通过`methor=“”`来调用class provider 里面的方法
- 用于动态SQL
- - `@Option` 里面的resultset为返回字段的属性（一定是SQL返回的字段属性 ）
```java
Mapper:
@Options(resultSets = "id, description, user_id, status ,create_time, update_time")
    @UpdateProvider(type = TagSqlProvider.class, method = "updateTag")
    int updateTag(Tag tag);

Provider.class:

/**
 * Update tag item via specific tag.
 *
 * @param tag the tag item need to update.
 * @Return the sql to execute.
 */
public class TagSqlProvider {
    public String updateTag(final Tag tag) {
        return new SQL() {
            {
                UPDATE("accounting_tag");
                if (tag.getDescription() != null) {
                    SET("description = #{description}");
                }
                if (tag.getStatus() != null) {
                    SET("status = #{status}");
                }
                if (tag.getUserId() != null) {
                    SET("user_id = #{userId}");
                }
                WHERE("id = #{id}");
            }

        }.toString();
    }
}

```
- update 
- - 部分更新：从数据库拿出来的数据和更新的数据进行对比，如果更新数据中一些属性为空的话，就要判断属性是否为空，是否和数据库里面对应的属性相等。如果相等则不更新，如果不相等，就把新的数据放入database里面，注意为更新数据里为null的属性不能放入，否则就会出错。
> - 对比就的和更新的数据，如果相同就不更新。
```java
public class RecordSqlProvider {
    /**
     * Update record item val specific record.
     *
     * @param record the record need to update
     * @return the sql to execute
     */
    public String updateRecord(final Record record) {
        return new SQL() {
            {
                UPDATE("accounting_record");
                if (record.getAmount() != null) {
                    SET("amount = #{amount}");
                }
                if (record.getCategory() != null) {
                    SET("category = #{category}");
                }
                if (record.getUserId() != null) {
                    SET("user_id= #{userID}");
                }
                if (record.getNote() != null) {
                    SET("note = #{note}");
                }
                if (record.getStatus() != null) {
                    SET("status = #{status}");
                }
            }
        }.toString();
    }

}

```
- - 全量更新：如果属性少的话可以进行全量更新,只要不是null的都更新。但是全量更新量大，不需要更新的情况下也全部更新，数据库压力就会增大。
```java
public class TagSqlProvider {
    /**
     * Update tag item via specific tag.
     *
     * @param tag the tat item need to update
     * @return the sql to execute
     */
    public String updateTag(final Tag tag) {
        return new SQL() {
            {
                UPDATE("accounting_tag");
                if (tag.getDescription() != null) {
                    SET("description = #{description}");
                }
                if (tag.getStatus() != null) {
                    SET("status = #{status}");
                }
                if (tag.getUserId() != null) {
                    SET("user_id = #{userId}");
                }
                WHERE("id = #{id}");
            }

        }.toString();
    }
``` 

## 分页
- 传统分页：一个页面展现的内容有限制，在底下或者顶部有页码选择其他的页面继续展示内容
- Token分页、流水分页、游标分页：不断地可以往下拉。
- 完成分页就要先解决排序问题。通过`ORDER BY`进行排序
- - `ASC`正序`SELECT * FROM accounting_tag where user_id = 1 ORDER BY create_time `

|id|	description|	user_id|	status|	create_time|	update_time|
|:----:|:----:|:----:|:----:|:----:|:----:|
1	|play boy	|1	|true	|2020-08-26 23:02:27.0	|2020-08-27 10:37:53.0
2	|play with friend	|1	|true	|2020-08-26 23:02:56.0	|<null>
3	|play game	|1	|true	|2020-08-26 23:13:45.0	|<null>
4	|eat	|1	|true	|2020-08-29 00:27:45.0	|<null>
5	|work	|1	|true	|2020-08-29 00:28:03.0	|<null>
6	|study	|1	|true	|2020-08-29 00:28:11.0	|<null>
7	|smoke	|1	|true	|2020-08-29 00:29:05.0	|<null>
8	|cat	|1	|true	|2020-08-29 00:29:22.0	|<null>

- - `DESC`倒序  
`SELECT * FROM accounting_tag where user_id = 1 ORDER BY create_time DESC`
- - 排序可以多个条件按先后优先次序进行
`
SELECT * FROM accounting_record ORDER BY amount DESC , id desc
`
- `LIMIT`进行分页
- - `select id ,description, user_id ,status, create_time, update_time FROM accounting_tag LIMIT 2,3` -> 3、4、5
- - `LIMIT 5` -> 1、2、3、4、5

- 分页插件 pagehelper-spring-boot
- Mybatis pageInfo
- - 在使用PageInfo的时候 `pageNum`、`pageSize`等需要用到的变量一定要和文档里面的名字相同，否则就不会执行分页等方法！！！

## 事务
- 原子性：事物内的业务全部完成才算成功，有一步失败都会回滚到最初。
- 一致性：满足数据库完整性的约束，就是建表的时候数据的类型约束、数据范围等，都会满足。
- 隔离性：写的内容没有提交前，其他读的对象是只能够执行没有改变前的数据内容，只有写的内容提交后才能够读到变更后的内容。
- 持久性：事物一旦提交，就会永久保存到数据库里面，即使系统发生故障，数据库也能够将数据恢复。
- read-commited 云服务器一般都会用这个，因为隔离级别比较高
- Console `begin`并不是开始，只有执行语句才是真正的开始
- 脏读： 没有commit 的数据被 另一个console 读取到了
- 读提交（READ COMMITTED）只能看到提交的事物，但是不可重复度，因为执行多次的查询可能会得到不同的结果（因为多次查询中间如果其他事务进行了commit，查询的结果就会出现不同）
- 可重复读：在同一个事务中，多次进行查询的到的结果也是一样的，即使在多次读取的中间有其他事务进行了commit，读取的结果也不会因为其他的commit而改变。
> - 但是可能会出现幻读：在其他事务已经插入了某个ID的数据且commit，在可重复读的事务里不能够查询到这个数据，还能够用同样的ID进行数据的插入和查询，没有出现主键冲突的现象，commit后查询能够看到同一个ID插入了两条不一样的数据
> - - 但是在mysql 8 后就禁用了，一般都不会出现
- 可串行化（SERIAIZABLE）：事务查询数据时，如果其他事务在进行数据的变更时候没有commit，就必须等待直到其他事务commit后才能够查询出数据结果，否则会一直等待到超时



## 问题
1.关于`Converter`
- Error:Consider defining a bean of type 'com.Procarihana.AccountingService.converter.commonToService.UserInfoCToSeConverter' in your configuration.
解决：
- 在`config`里面给转换器配置成bean
- @ConfigurationPropertiesBinding 注解是让 Spring Boot 知道使用该转换器做数据绑定。Mysql：`read log` 来保证事务的持久
```java
import com.Procarihana.AccountingService.converter.presisitenceToCommon.UserInfoPresToComConverter;
import org.springframework.boot.context.properties.ConfigurationPropertiesBinding;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;


@Configuration
public class ConverterConfig {
    @Bean
    @ConfigurationPropertiesBinding
    public UserInfoPresToComConverter userInfoPresToComConverter() {
        return new UserInfoPresToComConverter();
    }

    @Bean
    @ConfigurationPropertiesBinding
    public UserInfoCToSeConverter userInfoCToSeConverter() {
        return new UserInfoCToSeConverter();
    }
}
```
2. SpringBoot运行却得到404
- Error：This application has no explicit mapping for /error, so you are seeing this as a fallback.  Tue Aug 11 18:46:56 CST 2020 There was an unexpected error (type=Not Found, status=404). No message available
解决：运行业务逻辑前线运行HelloController看看有没有问题，如果没有就很大可能是Controller部分写错，例如GetMapping、参数的部分等等
注意↓等等的方式
```java
    @GetMapping("/{id}")
```
```java
 public UserInfo getUserInfoByUserId(@PathVariable("id") Long userId) {
 }
 ``` 
 3. Test 获得NullPointException
>解决：可以尝试通过Debug看运行用的是那个Junit，如果是Junit 4`@Test`引入的包出现错误
- Junit 5:import org.junit.jupiter.api.Test;
- Junit 4:org.junit.Test
4. Test 的时候java.lang.Exception: No runnable methods  
- 解决：可能是因为小天才类名和要测试的类名写一样了没有加Test。不然就是`@test`没有写或者包引错了。
5. `mockMvc.perform(get(""))`得到404  
- 解决：看看url是否一致，参数有没有错，还要看url格式是否正确
```java
mockMvc.perform(get("/v1.0/greeting/?name=1"))//一定是"/"开头！！！！！！
```
6. spotbugs:Class inherits equals() and uses Object.hashCode() 
- 大概率是插件不被识别
- 解决：在类前加`@EqualsAndHashCode(callSuper = true)`，搭配`@Component`和构造器一起使用效果更佳
- 使用`@Data`的时候，相当于`@Getter @Setter @RequiredArgsConstructor @ToString @EqualsAndHashCode`这5个注解的合集。

7. Bean named '&shiroFilterFactoryBean' is expected to be of type 'org.springframework.beans.factory.config.BeanPostProcessor' but was actually of type 'org.springframework.beans.factory.support.NullBean'
- 在写的过程中可能为了方便先把`@Bean` return null，但是这种情况下系统会判定这个null 是没有意义的，就会报错。

8.  No converter found capable of converting from type [java.lang.String] to type [java.util.Map<java.lang.String, org.springframework.boot.logging.LogLevel>]
- 在application.yml里编写获得日志的过程中格式不正确,要确保空格加上呀！ 
` com.procarihana.accounting:DEBUG` -> ` com.procarihana.accounting: DEBUG`

9. There is no config
ured chain under the name/key [/v1.0/session]
```json
{
    "timestamp": "2020-08-17T09:05:23.743+0000",
    "status": 500,
    "error": "Internal Server Error",
    "message": "There is no configured chain under the name/key [/v1.0/session].",
    "path": "/v1.0/session"
}
```
- 原因：  `shiroFilterDefinitionMap.put("/v1.0/session/","anon");`
- url后面不要随便加`/`不然就会误判而访问被拒绝
- 改为`shiroFilterDefinitionMap.put("/v1.0/session","anon");`就好了
10. Mybatis: org.apache.ibatis.reflection.reflectionexception: there is no getter for property named xxx in xx.class
- 原因：mapper 驼峰命名的变量名填写错误。VALUE `#{}`的内容是 在编译时变量的名字，而非对应的table的名字
```java
@Insert("INSERT into accounting_userinfo(username, password, salt, create_time, update_time) VALUES (#{username}, #{password}, #{salt}, #{create_time}, #{update_time})")
->
@Insert("INSERT into accounting_userinfo(username, password, salt, create_time, update_time) VALUES (#{username}, #{password}, #{salt}, #{createTime}, #{updateTime})")

```
11. request method 'get' not supported
- 添加shiro用于访问权限，同一url的post可以运行，但是get方法不能够运行(注：gei需要在path处输入param进行访问)
- 原因：shiro权限设置的url出问题
```java
        shiroFilterDefinitionMap.put("/v1.0/users", "anon"); //不需要登录就能访问

-->
        shiroFilterDefinitionMap.put("/v1.0/users/**", "anon"); //不需要登录就能访问

```
12. github ci 检测:OuterTypeFilename
- 可能是因为github上文件的名字和里面calss name 不相同造成的，所以即使是本地checkStyle 成功，但是ci 还是失败。要在github上面更改文件名字才成功。
- Travis ci 不香吗？
13. 调用`get`的时候出现
```json
"status": 415,
    "error": "Unsupported Media Type",
    "message": "Content type '' not supported"
```
- 有可能是因为包含了consumes` @GetMapping(path = "/{id}", produces = "application/json", consumes = "application/json")`
> - 删除后就能够执行` @GetMapping(path = "/{id}", produces = "application/json;charset=utf-8")`
14. Error: nested exception is org.springframework.`beans.factory.NoSuchBeanDefinitionException`: No qualifying bean of type 'com.procarihana.accounting.Dao.Mapper.RecordMapper'
- 可能是因为Mapper的interface 没有加上`@Mappere`
15. Error:元素类型 "foreach" 必须后跟属性规范 ">" 或 "/>"
- Mybatis mapper 的脚本里面<foreach>出现问题，有可能是因为每个属性之间没有空格！！！
- 属性： https://blog.csdn.net/isea533/article/details/21237175
```java
@Insert({"<script>",
        "INSERT INTO accounting_record_tag_mapping(record_id, tag_id, status) VALUES",
        "<foreach item='item' index='index' collection='recordTagMappings'",
        "open='('separator='),('close=')'>",
        "#{item.recordId},#{item.tagId},#{item.status}",
        "</foreach>",
        "</script>"})

        -->

        "<foreach item='item' index='index' collection='recordTagMappings'",
        "open='(' separator='),(' close=')'>",
        "#{item.recordId},#{item.tagId},#{item.status}",
        "</foreach>",
        
```

16. mybatis mapper 要是sql台场需要分开的话，需要特别注意空格，最好在每个分开的结尾添加空格！！！！！
```java
@Select("SELECT tag.id, tag.description, tag.status, tag.user_id, artm.record_id "
        + "FROM accounting_tag tag "
        + "LEFT JOIN accounting_record_tag_mapping artm "
        + "ON artm.tag_id = tag.id "
        + "WHERE artm.record_id = #{recordId}")
```



## SpringBoot添加事务
1. 在 Application 处添加 `@EnableTranscationManagerment` 
2. 在对应想开启事务管理的类或者方法上添加`@Transcation`注解


