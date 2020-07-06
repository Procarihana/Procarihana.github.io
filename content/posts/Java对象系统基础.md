---
title: "Java面向对象系统基础"
date: 2020-05-07T16:40:59+08:00
draft: false
---
#### 对象（引用类型）
- 对象是数据和行为的集合（主观能动性）
- 一切使用new运算符创建出来的都是对象
- - new Object（）
- - 特例： Integer  i=2/String s= "2" 自动装拆箱的时候后会自动new 出一个新的对象
- - 特例：new Object[]
####  对象组成？
- 所有的对象都在堆上分配
- 每个对象都包含⾃⼰的数据（成员变量）
1. 初始化   
  声明int age = 0; 和int age;是相同的，因为默认初始化的值为0 *【整数类型】*   
  String 等引用类型的为null
- 原⽣类型的成员
- 引⽤类型的成员
##### 栈、堆
栈：方法栈，main执行后需要用到的方法都会放到方法栈里面执行
堆：存放对象
#### 对象的构造函数
- 新建对象的唯⼀途径
- 在堆上分配空间
- 执⾏必要的初始化⼯作
- 执⾏构造器函数
- 如果没有任何构造器，编译器会偷偷摸摸帮你⽣成⼀个

#### 对象的方法
- 数据：有什么
- 方法：做什么

#### 方法的重载(overload)
- 重载（overload）与重写/覆盖（override）
1. 重载  
 方法名字相同但是参数不相同


        public class Cat{
         void printName(S){
              
          }
           
           void printName(tring s){
           }
       }

- 如何区分同名的不同重载⽅法？
- 根据类型  
调用时通过不同的参数区分


        cat.printName();
        cat.printName("null");
- 隐式转换
  自动匹配最合适的，按照转换的优先顺序



        public static void main(String agrs[]){
         Cat cat = new Cat();
            cat.s(1);
        }
        void s(int i){
           
        }//最优先
        void s(Integer i){
        
        }//其次
        void s(Number i){
        
        }
        void s(Object i){
        
        }
- 类型最优先匹配（如果匹配多个，如null 的话，可以为null进行强制类型转换，选择最优先的）
- 不能仅仅重载返回值  
因为方法的区别是通过方法名和变量区分的，且方法调用的时候是忽略返回值的，所以即使两个方法的名字和变量相同但返回值不一样，调用的时候会产生冲突，在编译的时候无法通过。但是运行字节码的时候是允许的。
- 不能为⽅法提供默认值  
  但是可以通过重载的方法实现


            public static void main(String[] agrs){
            Cat cat = new Cat();
            cat.meow();
            cat.meow("mimi");
            }
    
            public void meow(){
            meow("喵")；
        }
        public void meow(String value){
            System.out.print(value);
        }
    
#### 构造器的重载
- this()  
没有参数构造器可以通过重载，从而能够获得默认值
- 实例：HashMap的构造器重载



      //创建一只默认的猫，1岁，名叫张三
        Cat(){
            this("张三")；
        }
        //创建一支默认的猫，1岁，名叫name指定的名字
        Cat(String name){
         this(1,name);
        }
        //创建age和name指定的猫
        Cat (int age,String name){
            this.age = age;
            this.name = name;
        }
        public static void main(String[] agrs){
            new Cat();
        }
    
    
#### 对象的初始化
- 必要的初始化⼯作
- 静态成员的初始化
- 静态初始化块
- 成员的初始化
- 初始化块 {}
- 继续断点调试⼤法检查初始化顺序

#### 对象的⽣命周期
- 如果⼀直新建对象，内存会不会爆？  
可能不会
- 那对象的内存什么时候被回收？  
谁也不知道
- 对象的内存如何被回收？  
  不⽤管，GC垃圾回收器帮你⼲
- JVM怎么知道哪个对象没有被⽤到？
- 通过引⽤链（GC Roots）
  沿着GC Roots 可达的数据都是活对象，除此以外的都是垃圾
  但是没有被引用得不一定会被回收，取决于当前空间
#### 数组——特殊的对象
- 数组类是什么？为什么我从来没有⻅过？
- JVM为数组提供了特殊的处理⽅法
- 数组只有两个操作：[]与length的⻓度不可变
#### Tip
1. 如果在实行初始化的过程中调用方法的话，方法就会先于构造器执行，一个残缺的对象执行方法是一个不安全的行为
