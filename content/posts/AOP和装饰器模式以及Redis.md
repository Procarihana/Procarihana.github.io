---
title: "AOP和装饰器模式以及Redis"
date: 2020-09-04T16:40:35+08:00
draft: false
toc: false
images:
tags: [AOP,装饰器模式,Redis]
---

# 代理
- 静态代理，工程师编辑代理类代码，实现代理模式；在编译期就生成了代理类。
- 基于 JDK 实现动态代理，通过jdk提供的工具方法Proxy.newProxyInstance动态构建全新的代理类(继承Proxy类，并持有InvocationHandler接口引用 )字节码文件并实例化对象返回。(jdk动- 态代理是由java内部的反射机制来实例化代理对象，并代理的调用委托类方法)
- 基于CGlib 动态代理模式 基于继承被代理类生成代理子类，不用实现接口。只需要被代理类是非final 类即可。(cglib动态代理底层是借助asm字节码技术
- 基于 Aspectj 实现动态代理（修改目标类的字节，织入代理的字节，在程序编译的时候 插入动态代理的字节码，不会生成全新的Class ）
- 基于 instrumentation 实现动态代理（修改目标类的字节码、类装载的时候动态拦截去修改，基于javaagent） -javaagent:spring-instrument-4.3.8.RELEASE.jar （类装载的时候 插入动态代理的字节码，不会生成全新的Class ）
## CGlib
- 基于CGlib 技术动态代理代理类实现 (基于继承)
- Cglib是针对类来实现代理的，他的原理是对代理的目标类生成一个子类，并覆盖其中方法实现增强，因为底层是基于创建被代理类的一个子类，所以它避免了JDK动态代理类的缺陷。

但因为采用的是继承，所以不能对final修饰的类进行代理。final修饰的类不可继承。
## JDK 动态代理
- InvocationHandler接口被Proxy类回调处理，一般实现 InvocationHandler 接口的类具有委托类引用，接口方法 invoke 中添加公共代码并调用委托类的接口方法。(PersonInvocationHandler.java)
- JDK提供生成动态代理类的核心类Proxy ( JDK 提供的Proxy.java)

【https://cloud.tencent.com/developer/article/1461796】
#### 一个典型的基于JDK动态代理创建对象过程可分为以下四个步骤：
1、通过实现InvocationHandler接口创建自己的调用处理器 IvocationHandler handler = new InvocationHandlerImpl(...);

2、通过为Proxy类指定ClassLoader对象和一组interface代理类需要实现的接口，创建动态代理类类文件，默认JDK并不会保存这个文件到文件中；可以保存起来观察生成的代理类结构Class clazz = Proxy.getProxyClass(classLoader,new Class[]{...});

3、通过上面新建的代理clazz的反射机制获取动态代理类的一个构造函数，其构造函数入参类型是调用处理器接口(IvocationHandler)类型 Constructor constructor = clazz.getConstructor(new Class[]{InvocationHandler.class});

4、通过构造函数实例创建代理类实例，此时需将调用处理器对象作为参数被传入 Interface Proxy = (Interface)constructor.newInstance(new Object[] (handler)); 为了简化对象创建过程，Proxy类中的newInstance工具方法封装了2~4，只需两步即可完成代理对象的创建。
#### 基于JDK技术 动态代理类技术核心 Proxy类和一个 InvocationHandler 接口
- java的java.lang.reflect包下提供了Proxy类和一个 InvocationHandler 接口，这个类Proxy定义了生成JDK动态代理类的方法 getProxyClass(ClassLoader loader,Class<?>... interfaces)生成动态代理类,返回class实例代表一个class文件。可以保存该 class 文件查看jdk生成的代理类文件长什么样
- 该生成的动态代理类继承Proxy类，(重要特性) ，并实现公共接口。
- InvocationHandler这个接口 是被动态代理类回调的接口，我们所有需要增加的针对委托类的统一处理逻辑都增加到invoke 方法里面在调用委托类接口方法之前或之后 结束战斗。

#### 缺点
- JDK动态代理的代理类字节码在创建时，需要实现业务实现类所实现的接口作为参数。
- 如果业务实现类是没有实现接口而是直接定义业务方法的话，就无法使用JDK动态代理了。(JDK动态代理重要特点是代理接口) 并且，如果业务实现类中新增了接口中没有的方法，这些方法是无法被代理的（因为无法被调用）。

动态代理只能对接口产生代理，不能对类产生代理
# AOP
## AOP
- Aspect-Oriented Programming 面向切面编程
- 相当于OOP（面向对象编程）
- AOP是面向切面变成，关注一个统一的切面
- - 切面并不是对象和方法，而是执行方法的和方法之间的一瞬间、一个面
- AOP和Spring是不同的东西
- - 没有Spring容器AOP也是能够工作的
- - Spring 通过AOP来完成一些请求
#### AOP 适用的场景
- 需要统一处理的场景
- - 日志
- - 缓存
- - 鉴权
#### AOP的实现
-  JDK动态代理（Proxy）
- - 优点：⽅便，不需要依赖任何第三⽅库
- - 缺点：功能受限，只适⽤于接⼝
- - OOP完成这些需求的话，需要使用到装饰器模型
- CGLIB/ByteBuddy字节码⽣成
- - 优点：强⼤，不受接⼝的限制
- - 缺点：需要引⽤额外的第三⽅类库
- - 不能增强final类/final/private⽅法

#### AOP与Spring
- 在Spring中使⽤AOP实现Redis缓存
- Spring是如何切换JDK动态代理和CGLIB的？
- - spring.aop.proxy-target-class=true
>-    `proxy-target-class`属性值决定是基于接口的还是基于类的代理被创建。如果proxy-target-class 属性值被设置为true，那么基于类的代理将起作用（这时需要cglib库）。如果proxy-target-class属值被设置为false或者这个属性被省略，那么标准的JDK 基于接口的代理
- @Aspect声明切⾯
- -  @Before
- -  @After
- -  @Around(拦截目标) 包裹,拦截的条件可以通过自定义完成，不仅是通过注解标识

## Redis
- 服务器分布式的情况下，通过Redis进行缓存，数据就能够被所有服务器共用
- io（数据库）非常慢，数据在不改变的情况下进行缓存能够提高速度
- ⼴泛使⽤的内存缓存
- 常⻅的数据结构
- String/List/Set/Hash/ZSet
- 储存的方式 Redis<,>，和map非常像
- Redis为什么这么快
- - 完全基于内存
- -  优秀的数据结构设计
- -  单⼀线程，避免上下⽂切换开销
- -  事件驱动，⾮阻塞
- redis 端口为6379
#### java Object -> redis (序列化)
- java 自带的序列化：Serializability 接口，对象实现就能够自动进行序列化
- json 
## 装饰器模式
- Decorator pattern
- 动态地位一个对象增加功能，但不改变其结构
- 本质上是一个“包装”
- - 添加功能的时候不需要改变原本的对象，对对象进行包装，在执行方法的时候先执行包装的方法，再执行对象的方法
- 装饰器模式实际的是通过业务的接口，在实现的过程中执行添加的功能，真正实现业务的是另一个实现接口的类。在执行的时候`new 装饰器(new 实现业务（）)` 
#### 生成
- 定义一个抽象接口，接口为对象的功能
- 实现接口功能的类（接口方法默认是多态）
- - 接口而不是通过继承父类的方式
>- 接口实现没有什么成员变量，不需要实现真实的方法，而继承需要继承父类的成员变量，就会出现浪费的现象
#### 直接的实现login的方法
#### 直接的实现log的方法
- - 先写接口
```java
public interface DataService {
    String a (int i);
    String b (int i);
}
```
- - 再写实现，实现的过程中要直接手写log，如果有多个的话，就每一个都要写
```java
public class DataServiceImpl implements DataService{

    @Override
    public String a(int i) {
        System.out.println("a is called! parameter is :"+i);
        String result = UUID.randomUUID().toString();
        System.out.println("a is finished:"+result);
        return result;
    }
      @Override
    public String b(int i) {
        System.out.println("b is called! parameter is :"+i);
        String result = UUID.randomUUID().toString();
        System.out.println("b is finished:"+result);
        return result;
    }
}
```
- - 运行
```java
public class DataServiceMain {
    static DataService service = new DataServiceImpl();
    public static void main(String[] args) {
        service.a(1);
    }
}

```
### log装饰器
- - 同样是先写接口
- - 再写接口的实现
```java
public class DataServiceImpl implements DataService {

    @Override
    public String a(int i) {
        return UUID.randomUUID().toString();
    }

    @Override
    public String b(int i) {
        return UUID.randomUUID().toString();
    }
}
```
- - 装饰器实现接口,在override方法的时候，先实现log的方法，再完成接口需要完成的业务
```java
public class LogDecorator implements DataService {
    DataService delegate;//委托

    public LogDecorator(DataService delegate) {
        this.delegate = delegate;
    }

    @Override
    public String a(int i) {
        System.out.println("a is called! parameter is :" + i);
        String value = delegate.a(i);
        System.out.println("a is finished:" + value);
        return value;
    }

    @Override
    public String b(int i) {
        System.out.println("b is called! parameter is :" + i);
        String value = delegate.a(i);
        System.out.println("b is finished:" + value);
        return value;
    }
}
```
- - 运行
- - - 最后new出DataSerivce实例的时候，先new出Log的实例，还要记得new出实现真正实现业务接口的实例，不然log装饰器就不能够实现业务！
```java
public class DataServiceMain {
    static DataService service = new LogDecorator(new DataServiceImpl());
    public static void main(String[] args) {
        service.a(1);
    }
}
```
### Cache 装饰器
- 把调用过的方法缓存，之后就能够直接从缓存里面拿结果
- - 用Map储存的时候，在多线程的情景下会出现线程安全的问题
- 在logDecorate外添加一个缓存的装饰器，把方法运行的结果缓存起来
- - 首先要继承接口，把结果缓存到map里
```java
public class CacheDecorator implements DataService {
    private Map<String,String> cache = new HashMap<>();
    private DataService delegate;
    private String cacheLog = "cache result";

    public CacheDecorator(DataService delegate) {
        this.delegate = delegate;
    }

    @Override
    public String a(int i) {
        String cacheValue = cache.get("a");
        if (cacheValue == null){
            String realValue = delegate.a(i);
            cache.put("a",realValue);
            return realValue;
        }else {
            return cacheValue + cacheLog;
        }
    }
```
- - 实现
- - - 第二次调用方法的到的结果是从缓冲里拿出来，而且缓存只是缓存业务的结果没有缓存log，所以不会有log
```java
public class DataServiceMain {
    static DataService service = new CacheDecorator(new LogDecorator(new DataServiceImpl()));
    public static void main(String[] args) {
        System.out.println(service.a(1));
        System.out.println(service.a(1));
    }
}
```

## 实现AOP
### JDK动态代理
-  需要代理去实现
- -  Returns a proxy instance for the specified interfacesthat dispatches method invocations to the specified invocation handler.
- - 代理的实例为Object，所以要通过强制转换来完成实例对象的生成
- - 拦截的是一个实例
>- 对 InvocationHandler 看做一个中介类，中介类持有一个被代理对象，被Proxy类回调。在invoke方法中调用了被代理对象的相应方法。通过聚合方式持有被代理对象的引用，把客户端对invoke的调用最终都转为对被代理对象的调用。
> - 客户端代码通过代理类引用调用接口方法时，通过代理类关联的中介类对象引用来调用中介类对象的invoke方法，从而达到代理执行被代理对象的方法。也就是说，动态代理Proxy类提供了模板实现，对外提供扩展点，外部通过实现InvocationHandler接口将被代理类纳入JDK代理类Proxy。
- - 代理的实例为Object，所以要通过强制转换来完成业实例对象的生成
- - 拦截的是一个实例
```java
   @CallerSensitive
    public static Object newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h){
        Objects.requireNonNull(h);final Class<?> caller = System.getSecurityManager() == null? null
: Reflection.getCallerClass();

        /*
         * Look up or generate the designated proxy class and its constructor.
         */
        Constructor<?> cons = getProxyConstructor(caller, loader, interfaces);

        return newProxyInstance(caller, cons, h);
```

## 例子：log 
- 实现代理的接口，从而达到动态代理的功能 
```java
public class LogProxy implements InvocationHandler {
    DataService delegate;

    public LogProxy(DataService delegate) {
        this.delegate = delegate;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println(method.getName() + " is invoker:" + Arrays.toString(args));
        Object retValue = method.invoke(delegate, args);
        System.out.println(method.getName() + "  is finished:" + retValue);
        return retValue;
    }
}
```
- 实现

```java
public class DataServiceMain {
    static DataService service = new DataServiceImpl();
    public static void main(String[] args) {
        DataService dataService = (DataService) Proxy
            .newProxyInstance(service.getClass().getClassLoader(),
                new Class[] {DataService.class}, new LogProxy(service));

        dataService.a(1);
    }
}
```
### GCLib 动态代理
- 拦截的是一个实例方法
- 引入GCLib maven
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
- - 使用enhancer（增强）来实现拦截并调用拦截方法和要执行的业务方法
 * Generates dynamic subclasses to enable method interception. This
 * class started as a substitute for the standard Dynamic Proxy support
 * included with JDK 1.3, but one that allowed the proxies to extend a
 * concrete base class, in addition to implementing interfaces. The dynamically
 * generated subclasses override the non-final methods of the superclass and
 * have hooks which callback to user-defined interceptor
 * implementations.
- 动态生成↓的类，继承被拦截下来的类，从而能够通过强行转换的方式来生成业务和新增加功能的实例
![](/动态生成子类.png)

## 例子
- 生成一个拦截类，拦截一个具体的实例方法
```java
public class LogInterceptor implements MethodInterceptor {
    private  DataService delegate;

    public LogInterceptor(DataService delegate) {
        this.delegate = delegate;
    }

    @Override
    public Object intercept(Object o, Method method, Object[] objects, MethodProxy methodProxy) throws Throwable {
        System.out.println(method.getName() + " is invoked: " + Arrays.toString(objects));
        Object retValue = method.invoke(delegate,objects);
        System.out.println(method.getName()+" is finished: "+retValue);
        return retValue;
    }
}
```
- 实现，通过enhancer 完成
```java
public class Main {
    private static DataService service = new DataServiceImpl();

    public static void main(String[] args) {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(DataServiceImpl.class); //动态代理
        enhancer.setCallback(new LogInterceptor(service)); //动态代理被调用后，需要执行的方法

        DataService enhancedService = (DataService)enhancer.create();
        enhancedService.a(1);
    }
}

```

## Spring and AOP 通过GCLib 和 redis 实现缓存
#### maven
```
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
```
#### Srpring 配置,声明Spring动态配置由GCLib完成，而不是JDK动态配置完成,用于拦截类的方法
`spring.aop.proxy-target-class=true`
#### 实现缓存 -> 声明注解
- - 使得每一个声明这个注解的方法都进去缓存切面，进行方法拦截
- - `@Retention(RetentionPolicy.RUNTIME)`运行时才实现
```java
@Retention(RetentionPolicy.RUNTIME)
public @interface Cache {
}
```
#### 在需要运行redis 的地方加入`@Cache`来标记
- 通常会放在Services或者Manager这层
- 因为使用的是GCLib，所以要在实现业务的类处放置，而不能够在接口处放置
```java
@Component
public class GoodsRankManagerImpl {

    @Cache
    public List<RankItem> getGoodsRank() {
        return goodsRankDao.getGoodsRank();
    }
}
```
#### 建立缓存切面 CacheAspect
- - 使得所有拦截方法都进入这个切面的处理环境
- - @Aspect 声明切面
- - @Around(拦截目标) 包裹,拦截的条件可以通过自定义完成，不仅是通过注解标识
>- @Around(”@annotation(注解的packagename.注解name)“)
- - @Configuration Spring提供，声明这个类适合Spirng相关的配置
- - 使用Map进行缓存的储存，map只会被储存在运行的服务器里面，在分布式的情况下各个机器就不能够共享数据，而通过Redis储存数据则可以达到数据共享
```java
@Aspect
@Configuration
public class CacheAspect {
    Map<String, Object> cache = new HashMap<>();

    @Around("@annotation(com.github.hcsp.anno.Cache)")
    public Object cache(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature(); //获得方法名
        String methodName = signature.getName();
        Object cacheValue = cache.get(methodName);
        if (cacheValue != null) {
            System.out.println("Get value from cache");
            return cacheValue;
        } else {
            Object realValue = joinPoint.proceed();  //执行
            cache.put(methodName, realValue);
            System.out.println("Get value from database.");
            return realValue;
        }
    }
}
```


#### 通过Dcoker启动Redis
- 持久化储存要记得映射！
- 配置Spring
- - host 根据需求配置
```
spring.redis.host=localhost
spring.redis.port=6379
```
- maven
```
   <!-- spring-boot-starter-data-redis -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
```
#### 配置RedisTemplate
- - Helper class that simplifies Redis data access code. 简化redis 的数据操作
```java
@Configuration
public class AppConfig {
    @Bean
    RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory factory){
        RedisTemplate<String,Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(factory);
        return redisTemplate;
    }
}
```
#### RedisTemplate 数据的存储
- - opsForValue()有 set() 和get()两个方法来进行数据的存储，和map的get()、put()相似来尽心缓存数据的储存
```java
@Aspect
@Configuration
public class CacheAspect {
  //  Map<String, Object> cache = new HashMap<>();
    @Autowired
    RedisTemplate<String,Object> redisTemplate;
    @Around("@annotation(com.github.hcsp.anno.Cache)")
    public Object cache(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature(); //获得方法名
        String methodName = signature.getName();
        Object cacheValue = redisTemplate.opsForValue().get(methodName);
        if (cacheValue != null) {
            System.out.println("Get value from cache");
            return cacheValue;
        } else {
            Object realValue = joinPoint.proceed();  //执行
            redisTemplate.opsForValue().set(methodName, realValue);
            System.out.println("Get value from database.");
            return realValue;
        }
    }
}

```
#### 缓存时间限制（密钥超时时间限制）
- `Long timeout`为设置缓存的有效长
- `TimeUnit`设置有效时间的单位
- - 但是在测试的过程中呈现出时间控制不稳定的情况
```java

/**
	 * Set the {@code value} and expiration {@code timeout} for {@code key}.
	 *
	 * @param key must not be {@literal null}.
	 * @param value must not be {@literal null}.
	 * @param timeout the key expiration timeout.
	 * @param unit must not be {@literal null}.
	 * @see <a href="https://redis.io/commands/setex">Redis Documentation: SETEX</a>
	 */
	void set(K key, V value, long timeout, TimeUnit unit);
```
```java
Object realValue = joinPoint.proceed();
            redisTemplate.opsForValue().set(methodName, realValue, timeout, TimeUnit.SECONDS);
            System.out.println("Get value from database.");
            return realValue;
```

- 在`@Cache`里添加时间控制的方法
```java
@Retention(RetentionPolicy.RUNTIME)
public @interface Cache {
    int cacheSeconds() default 60;
}

```
- - 拦截方法出添加需要控制的时间长度
```jav
 @Cache(cacheSeconds = 1)
    @Override
    public List<RankItem> getGoodsRank() {
        return goodsRankDao.getGoodsRank();
    }
```
- - 实现方法拦截处添加getAnnotation（Cache.class）.cacheSeconds()来提取控制的时间

```java
  @Around("@annotation(com.github.hcsp.anno.Cache)")
    public Object cache(ProceedingJoinPoint joinPoint) throws Throwable {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        int expireTime = signature.getMethod().getAnnotation(Cache.class).cacheSeconds();
        redisTemplate.opsForValue().set(methodName, realValue, expireTime, TimeUnit.SECONDS);
```

#### 实现controller
- 通过ModleAndView 实现返回结果的渲染
- 用HashMap存储 返回结果的name 和 内容，返回模板文件name 和 modelHashmap 
```java
@RestController
public class Controller {
    private final goodsRankManager goodsRankManager;

    @Autowired
    public Controller(goodsRankManager goodsRankManager) {
        this.goodsRankManager = goodsRankManager;
    }

    @GetMapping(path = "/rank.htm", produces = "application/json")
    public ModelAndView getGoodsRank() {
        List<RankItem> items = goodsRankManager.getGoodsRank();
        HashMap<String, Object> model = new HashMap<>();
        model.put("items", items);
        return new ModelAndView("Rank", model);
    }
}
```

#### 缓存的对象要实现可以序列化
- 通过继承Serializable 可实现
```java
@Component
@Data
@AllArgsConstructor
@NoArgsConstructor
public class RankItem implements Serializable { //可序列化
    private String goodsName;
    private BigDecimal totalPrice;
}
```


## 问题
1.  使用到redis 的时候出现↓，且进入了死循环
```
reconnecting, last destination was localhost/127.0.0.1:6379
```
- 可以先看一下正在运行的容器里面的redis有没有出现port 输入错误的问题……


- 设计模式 应对变化的部分
- 面向接口
- 组合比继承好
- - 避免添加功能的时候需要不断地进行继承添加接口来完成
# 装饰器模式
-   

