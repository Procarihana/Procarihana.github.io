---
title: "Mybatis"
date: 2020-09-01T10:57:12+08:00
draft: true
toc: false
images:
tags: [Mybatis]

---
## Environment
#### DataSource
- dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。

- 大多数 MyBatis 应用程序会按示例中的例子来配置数据源。虽然数据源配置是可选的，但如果要启用延迟加载特性，就必须配置数据源。
有三种内建的数据源类型（也就是 type="[UNPOOLED|POOLED|JNDI]"）：

- - UNPOOLED– 这个数据源的实现会每次请求时打开和关闭连接。虽然有点慢，但对那些数据库连接可用性要求不高的简单应用程序来说，是一个很好的选择。 性能表现则依赖于使用的数据库，对某些数据库来说，使用连接池并不重要，这个配置就很适合这种情形。UNPOOLED 类型的数据源仅仅需要配置以下 5 种属性：

>- driver – 这是 JDBC 驱动的 Java 类全限定名（并不是 JDBC 驱动中可能包含的数据源类）。
>- url – 这是数据库的 JDBC URL 地址。
>- username – 登录数据库的用户名。
>- password – 登录数据库的密码。
>- defaultTransactionIsolationLevel – 默认的连接事务隔离级别。
>- defaultNetworkTimeout – 等待数据库操作完成的默认网络超时时间（单位：毫秒）。查看 java.sql.Connection#setNetworkTimeout() 的 API 文档以获取更多信息。
>- 作为可选项，你也可以传递属性给数据库驱动。只需在属性名加上“driver.”前缀即可，例如：driver.encoding=UTF8这将通过 DriverManager.getConnection(url, driverProperties) 方法传递值为 UTF8 的 encoding 属性给数据库驱动。

- - POOLED– 这种数据源的实现利用“池”的概念将 JDBC 连接对象组织起来，避免了创建新的连接实例时所必需的初始化和认证时间。 这种处理方式很流行，能使并发 Web 应用快速响应请求。

除了上述提到 UNPOOLED 下的属性外，还有更多属性用来配置 POOLED 的数据源：

>- poolMaximumActiveConnections – 在任意时间可存在的活动（正在使用）连接数量，默认值：10
>- poolMaximumIdleConnections – 任意时间可能存在的空闲连接数。
>- poolMaximumCheckoutTime – 在被强制返回之前，池中连接被检出（checked out）时间，默认值：20000 毫秒（即 20 秒）
>-  poolTimeToWait – 这是一个底层设置，如果获取连接花费了相当长的时间，连接池会打印状态日志并重新尝试获取一个连接（避免在误配置的情况下一直失败且不打印日志），默认值：20000 毫秒（即 20 秒）。
poolMaximumLocalBadConnectionTolerance – 这是一个关于坏连接容忍度的底层设置， 作用于每一个尝试从缓存池获取连接的线程。 如果这个线程获取到的是一个坏的连接，那么这个数据源允许这个线程尝试重新获取一个新的连接，但是这个重新尝试的次数不应该超过 poolMaximumIdleConnections 与 poolMaximumLocalBadConnectionTolerance 之和。 默认值：3（新增于 3.4.5）
poolPingQuery – 发送到数据库的侦测查询，用来检验连接是否正常工作并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动出错时返回恰当的错误消息。
>- poolPingEnabled – 是否启用侦测查询。若开启，需要设置 poolPingQuery 属性为一个可执行的 SQL 语句（最好是一个速度非常快的 SQL 语句），默认值：false。
>- poolPingConnectionsNotUsedFor – 配置 poolPingQuery 的频率。可以被设置为和数据库连接超时时间一样，来避免不必要的侦测，默认值：0（即所有连接每一时刻都被侦测 — 当然仅当 poolPingEnabled 为 true 时适用）。
- - JNDI – 这个数据源实现是为了能在如 EJB 或应用服务器这类容器中使用，容器可以集中或在外部配置数据源，然后放置一个 JNDI 上下文的数据源引用。这种数据源配置只需要两个属性：

>- initial_context – 这个属性用来在 InitialContext 中寻找上下文（即，initialContext.lookup(initial_context)）。这是个可选属性，如果忽略，那么将会直接从 InitialContext 中寻找 data_source 属性。
>- data_source – 这是引用数据源实例位置的上下文路径。提供了 initial_context 配置时会在其返回的上下文中进行查找，没有提供时则直接在 InitialContext 中查找。

https://mybatis.org/mybatis-3/zh/configuration.html


## 在SpringBoot里配置Mybatis
#### Maven配置
- SpringBoot Mybatis Maven 
```java
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```
#### Spring的xml配置
- 完成Mybatis和Spring的装配需要`SqlSessionFactory` 和 `mapper接口`
- Mybatis能够自动检测`DataSource`，然后自动生成`SqlSessionFactory`,不需要额外装配。
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
#### Mybatis 配置
- MybatisConfig.xml
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
#### Spring 处 mybatisConfig 配置
  
  ```
  mybatis.config-location =  classpath:db/mybatis/Mybatis-config.xml
  ```
#### mapper.xml
- - 注意`resultMap`和`resultType`的区别！
```java
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="db.mybatis.mapper.rankItemsMapper">
 <select id="getRank" resultMap="RankMap">
```
#### Dao
- - sqlSession.setLest/setOne("select id")
```java
@Repository
public class GoodsRankDaoImpl implements goodsRankDao {
    @Autowired
    SqlSession sqlSession;
    @Override
    public List<RankItem> getGoodsRank() {
        return sqlSession.selectList("db.mybatis.mapper.rankItemsMapper.getRank");
    }
}
```
## 整理
1. select 出来的多组数据，可暂时在mybatis 里新建一个map 储存
```
<select id="getRank" resultMap="RankMap">
        select goods.name as goods_name,
        (case when sum(o.price * o .quantity) is null then 0 else sum(o.price * o .quantity) end) as total_price
        from `order` o right
        join goods
        on goods.id = o.goods_id
        group by goods.id order by total_price desc

    </select>

    <resultMap id="RankMap" type="com.github.hcsp.Moudle.RankItem">
        <result property="goodsName" column="goods_name"/>
        <result property="totalPrice" column="total_price"/>
    </resultMap>
```
- 再通过`sqlsession. selectList()`来得到这组数据


|||
|--|--|--|
|column|数据库中的列名，或者是列的别名。一般情况下，这和传递给 resultSet.getString(columnName) 方法的参数一样。|
javaType|	一个 Java 类的完全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。|
jdbcType|	JDBC 类型，所支持的 JDBC 类型参见这个表格之前的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可能存在空值的列指定这个类型。|
typeHandler	|我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的完全限定名，或者是类型别名。|
select|	用于加载复杂类型属性的映射语句的 ID，它会从 column 属性中指定的列检索数据，作为参数传递给此 select 语句。具体请参考关联元素。|
resultMap	|结果映射的 ID，可以将嵌套的结果集映射到一个合适的对象树中。 它可以作为使用额外 select 语句的替代方案。它可以将多表连接操作的结果映射成一个单一的 ResultSet。这样的 ResultSet 将会将包含重复或部分数据重复的结果集。为了将结果集正确地映射到嵌套的对象树中，MyBatis 允许你 “串联”结果映射，以便解决嵌套结果集的问题。想了解更多内容，请参考下面的关联元素。|
name|	构造方法形参的名字。从 3.4.3 版本开始，通过指定具体的参数名，你可以以任意顺序写入 arg 元素。参看上面的解释。|


  ## 问题
  1. 在select join 的时候，select 出来的列最好as出别名，方便之后和对象数据交互的时候容易写出property
 
  ```java
  <mapper namespace="db.mybatis.mapper.rankItemsMapper">
    <select id="getRank" resultMap="RankMap">
        select goods.name as goods_name, sum(o.price * o .quantity)as total_price from
        `order` o right join goods
        on goods.id = o.goods_id
        group by goods.id order by total_price desc
    </select>

    <resultMap id="RankMap" type="com.github.hcsp.Moudle.RankItem">
        <result property="goodsName" column="goods_name"/>
        <result property="totalPrice" column="total_price"/>
    </resultMap>
    </mapper>
```
