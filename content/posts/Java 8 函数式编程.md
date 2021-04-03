---
title: "Java 8 函数式编程"
date: 2020-09-28T16:28:56+08:00
draft: false
toc: false
images:
tags: ["Lambda",""]
---
函数式编程
1、减少工作量
2、提高效率
3、减少Bug  

---
`Java8的新引入，包含函数式的设计，接口都有@FunctionalInterface的注解。`注解在接口层面，且注解的接口要有且仅有一个抽象方法。--> 

---

## Lambada 表达式
**Lambda允许把函数作为一个方法的参数（函数）作为参数传递进方法中**
- Filter 
- Pradicate 判断，用来判断一个东西是否满足条件
>- （user -> user.id%2==0）// 方法列表->方法体，如果方法体太多要加括号，但一般精简.
>- 满足 方法列表能够映射成布尔值，就能够转化成函数接口
>- 方法应用有名字能够解释方法的用处，函数接口简单，但是没有任何注释；
- 满足类名`::`静态方法名字，且方法为布尔函数的时候，可以使用`::`


接口|输入 -> 输出|  |接口|输入 -> 输出|
|--|--|--|--|--|
Supplier `<Object>`|虚无 -> Object|  |Function`<User,String>`|User -> String|
Lambad`() -> new Object()`|void -> Object||Lambda `user -> user.getName()`|User -> String|
static Object create()|void -> Object||static String getName(User user)|User -> String|
Object :: new | void -> Object || User::getName|User -> String
- - -
- 任何只包含一个抽象方法的接口都可以被自动转换为函数接口
- 抽象类：没有方法体
---
- Supplier :从虚空里获得一个对象 --> void -> OBject
- new 也同样是从虚空获得一个对象
```java
public static void main (String [] agrs){
    create(() -> new Object());
    create(() -> "");
    create(() -> new User(1,"A"));
    create(Object :: new);
}

private static Object create(Supplier<Object> supplier){
    return supplier.get();
}
```
- BiConsumer
- -  有两个映射相加后变成一个新的映射，类似于a+b=c
- - 接口接收两个泛型参数，跟Consumer一样，都有一个 accept方法，只不过，这里的，接收两个泛型参数，对这两个参数做下消费处理；使用这个函数式接口的终端操作有map的遍历
- - BiConsumer函数接口还有一个默认函数，andThen，接收一个BiConsumer接口，先执行本接口的，再执行传入的参数。
```java

@FunctionalInterface
public interface BiConsumer<T, U> {
 
    
    void accept(T t, U u);
 
	/**本接口中的accept先执行，传入的BiConsumer 接口类型的参数，后执行accept*/
    default BiConsumer<T, U> andThen(BiConsumer<? super T, ? super U> after) {
        Objects.requireNonNull(after);
 
        return (l, r) -> {
            accept(l, r);
            after.accept(l, r);
        };
    }
}
```
- - HashMap
>- Map接口的终端操作，forEach的参数就是BiConsumer函数接口，对HashMap 的数据进行消费；

```java
public static void main(String[] args) {
        Map<Integer, Integer> map = new HashMap<>();
        map.put(1, 1);
        map.put(2, 2);
        map.put(3, 3);
        map.forEach((k, v) -> {
            System.out.println(k);
            System.out.println(v);
        });
    }
```

|||
|--|--|
BiConsumer<T,U>| 一个接受两个输入参数的操作，并且不返回任何记过|
BiFunction<T,U,R>|一个接受两个输入参数的方法，并且返回一个结果|
Consumer`<T>`|接受一个参数并且无返回操作
Predicate`<T>`| 接受一个输入值，返回一个布尔值结果
Supplier`<T>`| 无参数，返回一个结果