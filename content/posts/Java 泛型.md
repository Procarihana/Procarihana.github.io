---
title: "Java 泛型"
date: 2020-09-28T19:12:01+08:00
draft: false
toc: false
images:
tags: ["",""]
---

- 上界通配符（Upper Bounds Wildcards）< ? extends T > 
- 下界通配符（Lower Bouds Wildcards） <? super T>

#### 通配符和边界的作用
##### 边界让不同泛型转换更加容易，但是部分容易功能容易失效
- 上界通配符 : 一个能放水果以及其子类的盘子
>- 苹果继承水果，但是能放水果的盘子不能够放苹果，因为能放苹果的盘子并非继承能放水果的盘子，他们之间没有继承关系
>- 为了能够让盘子放苹果，需要一个`Plate<? extend Fruit>`的盘子，这个盘子能够放Fruit 的所有子类
- 下界通配符 ：一个能放水果Fruit 以及其父类的盘子
>- Fruit 继承 Food， 所以`Plate<? super Fruit>` 的盘子可以放 `Plate< Food>`, 因为 Fruit 作为下界限，所以 Apple 就不能放进去了 
---
- 上界不能往里存，只能往外去
- - 因为编译器只知道容器内是Fruit 或者其子类，但具体类型是不知道的，所以用
`Plate<Apple>` 赋值之后，plate 并没有标上Apple，而是标上一个占位符`CAP#1`,表示捕获一个Fruit或Fruit子类，具体是什么类并不知道，只用代号代替。所以之后无论添加什么类型都无法判断是否和apple 是统一类型，因为只有代号。
```java
Plate<? extends super Fruit> p = new Plate<Fruit>(new Apple());
 //不能存入任何元素
 p.set(new Fruit()); //ERROR
 p.set(new Apple()); //ERROR
 //可以读出且能放在FRUIT和其父类
 Apple newFruit1 = p.get(); //ERROR
 Fruit newFruit2 = p.get();
 Object newFruit3 = p.get();
```
- 下界不影响往里存，但往外去只能够放在Object对象里
- - 因为下界规定了元素的最小粒度的下限，实际上是放松了容器元素的类型控制。既然元素Fruit的基类，那往里存粒度比Fruit小的都可以。但是往外读取元素就只能所有类的基类Object对象才能装下，这样的话元素类型信息就全部丢失

```java
Plate<? super Fruit> p = new Plate<Fruit>(new Fruit()));
 //往里存元素正常
 p.set(new Fruit());
 p.set(new Apple());
 //往外拿只能够放在Object里面
 Apple newFruit1 = p.get(); //ERROR
 Fruit newFruit2 = p.get();//ERROR
 Object newFruit3 = p.get();
```
#### 通配符`<?>`和`<T>`区别
- `<?>` 没有约束，只是表示有一个东西，但是不知道是什么
- `<T>` 表示T代表一种类型，可以使String，Integer等等，一旦规定了就确定了T是这个类型
