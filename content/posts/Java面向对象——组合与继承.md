---
title: "Java面向对象——组合与继承"
date: 2020-05-11T17:19:33+08:00
draft: false
---
## 三大特征
##### 封装
##### 继承
public class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public void sayMyName() {
        System.out.println("我的名字是" + name);
    }
    public class Cat extends Animal{

    public Cat(String name) {
        super(name);
    }

    public void meow() {
        System.out.println("喵" + name);
    }
    }
    public class Main {
    public static void main(String[] args) {
        Cat cat = new Cat("ABC");
        cat.meow();
        cat.sayMyName();
        }
##### 多态

## 继承

### DRY（Donot Repeat Yourself）
-  事不过三，三则重构
### 继承的本质是提炼出公用代码，避免重复
继承一个类就会自动继承类的行为

## Java的继承体系
### 单根继承（单一继承）：声明一个Class的时候，不声明任何东西都会继承Object
#### 优点
- 保证所有对象都拥有同一种行为，方便处理


- 对比C++的多重继承
### Object类有那些需要了解的方法？
- equals（）

判断两个对象是不是同一个对象（地址、内存）



    Object a = new Object();
    Object b = new Object();
    a.equals(b);
    //虽然不是两个相同的对象，但是包含的内容相同，所以认为是相等的

    
==equals()andhashcoad()==
    
    public class Order {
    Integer id;
    String name;
    //id一样，即使地址不一样，就认为是同一个订单

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Order order = (Order) o;
        return id.equals(order.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }

当你覆盖Equals方法的时候，要同时覆盖Hashcode
“==”判断地址是否相同；/原生数据类型则是比较的是值否向同

- toString（）
- 对一个对象提供字符串表示
- 所有的对象都有toString方法（所有对象都继承Object类）


     public static void main(String[] args) {
        Cat a = new Cat();
        System.out.println(a);
    public class Cat {
    String name = "hey";

    @Override
    public String toString() {
        return "Cat{" +
                "name='" + name + '\'' +
                '}';
    }

## 类的结构与初始化顺序
- 子类拥有父类的一切数据和行为  
（即使继承的子类一行代码也没有，也可以调用父类的方法）
- 子类可以继承父类的父类
- 父类先于子类生成构造器，因为只有调用父类的方法后才能够完成子类的构造器的
- 必须拥有匹配的构造器
- - super关键字
- - -  需要自行生成一个子类的构造器的时候，就必须要声明一个和父类构造器参数相同的构造器。构造器第一句一定是超类构造器super（父类参数）


初始化顺序
构造器（不声明任何构造器的化，编译器会自动生成）
不在父类声明构造器时，父类和子类会有自动构造器生成
如果在父类声明构造器，则需要在子类声明super构造器

1. 父类初始化语句
2. 父类初始化块
3. 父类的constructor
4. 子类的……
A继承B继承C  
C先被创造出来，接着到B，最后到A

## 实例方法的Override
- 又称为覆盖/重写
- 永远使用@Override注解来防手残

            public static main(String[]args){
                String 1 = "ABC";
                String 2 = "ABC";
                1.equals(2);
            }
            在执行的过程中可以看到判断相等的时候会看到当前的Integer.equals方法会覆盖object.equal方法(判断value是否相同)
            

## 模版方法
- 提供一个“模版”，实现可以覆盖模版的全部或者部分
子类可以调用父类的方法，在调用的过程中通过覆盖达到完成需求的目的，其他不改动的沿用父类的模版执行

- 通过@Override可以模板中的一个流程，实现自定义的功能
- 在Override里面通过super.父类的方法，从而实现先执行父类的模板再实现自定义
>- 1. 什么是类加载器？

- 把类加载的过程放到Java虚拟机外部去实现，让应用程序决定如何去获取所需要的类。实现这个动作的代码模块称为“类加载器”。  

2. 有哪些类加载器，分别加载哪些类

- 类加载器按照层次，从顶层到底层，分为以下三种：
(1)启动类加载器 : 它用来加载 Java 的核心库，比如String、System这些类
(2)扩展类加载器 : 它用来加载 Java 的扩展库。
(3) 应用程序类加载器 : 负责加载用户类路径上所指定的类库，一般来说，Java 应用的类都是由它来完成加载的。

3. 双亲委派模型

- 我们应用程序都是由以上三种类加载器互相配合进行加载的，还可以加入自己定义的类加载器。称为 类加载器的双亲委派模型 ，这里类加载器之间的父子关系一般不会以继承的关系来实现，而是都使用 组合关系 来复用父加载器的。

4. 双亲委托模型的工作原理

- 是当一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把这个请求委派给父类加载器去完成，每一个层次的类加载都是如此，因此所有的加载请求最终都应该传送到顶层的启动类加载器中，只有当父加载器反馈自己无法加载这个加载请求的时候，子加载器才会尝试自己去加载。

5. 使用双亲委派模型好处？（原因）

第一：可以避免重复加载，当父亲已经加载了该类的时候，子类不需要再次加载。

第二：考虑到安全因素，如果不使用这种委托模式，那我们就可以随时使用自定义的String来动态替代java核心api中定义类型，这样会存在非常大的安全隐患，而双亲委托的方式，就可以避免这种情况，因为String已经在启动时被加载，所以用户自定义类是无法加载一个自定义的类装载器。
## 向上/向下转型
#### 一个子类类型的对象永远是一个父类类型的对象
- 正如一只猫同时也是一只动物，同时也是一个对象
- instanceof 判断类型
- null instanceof？ == false

Eg：
object <-- Number <-- Integer

a instanceod Integer/Number/Object (a是不是Integer/Number/Object的一个实例)

### **Null不是任何东西的对象**
   
   null instanceof a(必须是一个类，所以不能是null) ==>false

##### 因此，需要一个父类型时，总可以传递一个子类型
因为所有的子类都是父类的实例
null可以被赋值给任何引用类型的对象，因此null也可以传递给父类  
AnimalName（new Cat（name:"喵"））
##### 转型

子类转换为父类是安全的，但是父类转为子类是危险的（小的放进大的里面是安全的，大放小则有可能溢出）

     
     
     Animal a = new Animal();
     //类似于int a = 1；
     //long i = a；
     //int b = （int）i；
     sayAnimalName((Dog)a);//通过强制传换 
     
     public static void sayAnimalName(Dog animal){
    
     }




        public class Classifier {
        public static void main(String[] args) {
        List<Object> list = Arrays.asList("0", 1, 2L, "3", new Object());
        List<Number> numbers = new ArrayList<>();
        List<String> strings = new ArrayList<>();
        List<Object> others = new ArrayList<>();

        classify(list, numbers, strings, others);

        System.out.println("numbers = " + numbers);
        System.out.println("strings = " + strings);
        System.out.println("others = " + others);
    }

    /**
     * 给定一个包含任意对象的列表，将其按照以下方式分类： 如果对象是Number类型，将其放入numberList； 如果对象是String类型，将其放入stringList；
     * 否则，将其放入otherList。
     *
     * @param list 给定的包含任意对象的列表
     * @param numberList 用于接收所有Number对象的列表
     * @param stringList 用于接收所有String对象的列表
     * @param otherList 用于接收其余所有类型对象的列表
     */
    public static void classify(
            List<Object> list,
            List<Number> numberList,
            List<String> stringList,
            List<Object> otherList) {
        for (Object element : list ){//先游历数据一遍
            if (element instanceof Number){
                numberList.add((Number) element);
            }else if (element instanceof String){
                stringList.add((String)element);
                }else otherList.add(element);
            }
        }

        }

## Super关键字
调用父类的方法（包括构造函数）

## final关键字

##### final声明变量，变量成为不可变的（必须初始化），保证线程安全


- [ ] final int i;
- [x] -

      public Main(int i){
      this.i = 1;
      //this.i = 2;不行，因为final之后只能够赋值一次，之后就不能够被改写了
      }
    
    final int i;
    
    

- 局部变量/方法参数
- 成员变量
##### final 声明对象
    
    Date date = new Date();
    f(date)
    
     private void f(final Date date){
         //对象只是一个地址，final对象指向的地址改变不可以
         //但是指向的内容可以改变
         date = new Date();//不行
         date.setTime();//数据可变
     }
- 常量与单例  
通过final 声明常量，使用对象的时候不需要在此new一个新的对象，可以直接使用这个常量。引用常量的时候可以通过名字引用，能够快速知道常量的意义。


            public static final int MAX_VALUE = 100;
            public void maxValue(){
            for(int i = 0 ; i < MAX_VALUE ; i++){
        
            }  

###### 单例
- 不能被继承，因为是final
- - 方法命名：驼峰形式 maxValue
- - 常量命名：全部大写 MAX_VALUE


        private static final World SINGLEON_INSTANCE = new World();//改变成public就可以被大家所引用
        private World(){//构造器私有化，保证没有人能够通过public的方法调用
        
        }
        /*但是还是能调用创建新的对象
        1. 通过反射调用private的构造器
        2. 使用序列化和反序列化
        */
    
Boolean Integer ……

        private static final World SINGLEON_INSTANCE = new World();
        public static void World getInstance(){
        return SINGLETON_INSTANCE;//通过工厂方法动态决定是否调用单例，也可作为与外界接触的接口
    }


final优点：
1. 保证变量被使用的时候是最开始的值
2. 线程是安全的，不用担心多线程带来的问题
3. 类不会被继承，覆盖，重写，不会被改变，保证约定不会被打破，不用担心多态

##### final在方法上的声明：禁止继承/覆盖/重写此方法
##### final在类声明上的使用：禁止继承此类

- 继承提供了灵活性，也埋下了隐患
- 为什么String/Integer等类是final的？  
避免一些特殊的常量被恶意继承，例如Integer 的MAX_VALUE、MIX_VALUE 破坏约定

## 组合
##### 继承：is - a
- 使用继承是为了复用方法
##### 组合：has - a
- 可以复用多份代码

    
        class Cat {
            Bell bell;
        }
        private class Bell{
            
        }
        class Home{
            Cat cat;
        }
- 但是实际中，二者的界限有时候很难区分
- CountingSet
- 完全不实用继承
git clone https://github.com/victoriadrake/hugo-theme-sam.git themes/sam
