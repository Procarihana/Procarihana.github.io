---
title: "Java面向对象  接口和抽象类"
date: 2020-09-22T18:02:40+08:00
draft: true
toc: false
images:
tags: ["",""]
---

## 问题：如何禁止默认实现

强制要求覆盖方法


- 抛出异常

在空的方法里面设置UnsupportedOperationException（）；
    
- - 缺点：编译的时候不会报错，运行的时候抛出异常
- - 原因：abstruct是一个抽象集合，有时候这个集合不可变，不可以往里面添加（不支持修改，不可变）
- 抽象化
抽象方法必须在抽象类中
抽象方法不能够实例化

强制提供默

- 抽象类

可以有成员，可以有构造器，可以有实例方法
1. 实现
2. 抽象


Abstruct 有时候是不可变，不可以添加东西
- 接口

## 抽象类
**除了用abstruct声明之外，和其他类没有本质上的区别**

- 不可实例化  
没有方法体的方法 
- 可以拥有抽象的方法 abstruct void methon（）；
- 可以是实例化的东西一定要补全所有的方法体   
因为抽象方法没有执行的内容，执行也没有内容可执行，所以必须要补全方法体
- 可以包含抽象方法   **非private/static**
- - 抽象方法目的是让人继承
- - 继承多态只发生在实例里面，因此不能用static，没有意义
- - private不能被子类访问继承，继承也没有用
- 可以包含普通类的任何东西  
内部类、成员变量……

AbstructCollection  集合
AbstructList 列表（有序）

addAll就是逐个调用add（）

---
##  接口 Interface
- 接口部分的实现了多继承
- 接口不是类
- 接口只代表一种功能  
- 经典的接口定义：只描述接口有什么功能，但是不给出具体的实现  
 （但可以包含方法实现）
- 接口中的（成员变量）默认都是public（访问限定符） 
- - static 用于多态（实例方法）所以不能使用
- - final不可改变，不能用于继承，所以也不可使用
- 接口能有成员变量，但是有限制（默认为public static final），作为常量命名就需要全部大写
- instanceof 不仅可以检测是否是一个类，还能够检测是否是一个接口
#### 接口的扩展
- 接口可以通过集成扩展接口  
继承另外一个接口，继承后能够拥有原先接口的所有方法，也要实现它所有的方法
- 一个类只能继承一个类，但是却能实现若干的接口
---

## 接口可以包含？
-  若干个方法（默认public）
-  若干个常量（默认public static final）
- - 在接口里面的变量默认都是public static final 的，它们是公共的,静态的,最终的常量.相当于全局常量，可以直接省略修饰符。
实现类可以直接访问接口中的变量
- extends 接口
- 默认方法
- - Since Java 8
- - 是一种妥协的产物（默认方法）[不破坏向后兼容]
- - - 通过添加 default void sort 而默认的有方法体的方法，即使使用接口没有实现这个默认方法，也可以正常运行，还能够获得这个实现
- - 一个接口发布出去，就不能增添或减少方法、成员
- - 因为所有的类都必须实现接口的所有方法，会导致之前实现的类都报错，打破向后兼容性
**但是会出现二异性** 
- - 实现方法放在class 里面，就能够避免多个实现（多继承）带来的矛盾（避免二义性），从而提高面向对象的灵活性
   
- - 如果不想实现所有的方法的话，可以声明为抽象类，可以继承接口，但是没有实现，把实现留给继承类
- 可以用来实现mixin
- 菱形继承
---
#### 抽象类？接口？如何选择
- 需要赋用代码的时候，用抽象类
- 只描述一种功能的时候用接口，接口不能包含方法实现，- 接口的抽象化更高

- 抽象类和接口都是继承   
多态只发生在实例方法间，final和static是不能用的

共同点：
1. 抽象，不可实例化
2. 可包含抽象方法（没有方法体，非static/public/final）

异
1. 抽象类是类，可以包含类的一切东西；  
 接口只能包含受限的成员和方法（public static final[常量]）没有方法体的抽象方法/dafule
2. 抽象类只可以单一继承；一个类只能继承一个抽象类或者一个普通的类  
接口可以多继承，甚至继承多次（强制类型转化）  
最大成都的灵活性，最大程度的复用（大家都能够使用）


            public class World {
            // 现在有若干种对象，请尝试使用接口和抽象类将它们建造成类型体系
            // 以最大限度的复用、简化代码
            public static List<Object> objects =
                Arrays.asList(new 麻雀(), new 喜鹊(), new 蝴蝶(), new 飞机(), new 救护车(), new 猫(), new 狗());
              //在建造成类型体系后，请尝试化简这个啰嗦的方法，体会多态带来的好处
             public static void 会飞的东西飞() {
            /**
            for (Object obj : objects) {
                if (obj instanceof 麻雀) {
                    ((麻雀) obj).飞();
                } else if (obj instanceof 喜鹊) {
                    ((喜鹊) obj).飞();
                } else if (obj instanceof 蝴蝶) {
                 ((蝴蝶) obj).飞();
                } else if (obj instanceof 飞机) {
                 ((飞机) obj).飞();
                }
                */
                if (obj instanceof 会飞的东西) {
                    ((会飞的东西) obj).飞();
                    }
                }
            }
        // 在建造成类型体系后，请尝试化简这个啰嗦的方法，体会多态带来的好处
    public static void 会叫的东西叫() {
        /*
        for (Object obj : objects) {
            if (obj instanceof 麻雀) {
                ((麻雀) obj).叫();
            } else if (obj instanceof 喜鹊) {
                ((喜鹊) obj).叫();
            } else if (obj instanceof 救护车) {
                ((救护车) obj).叫();
            } else if (obj instanceof 猫) {
                ((猫) obj).叫();
            } else if (obj instanceof 狗) {
                ((狗) obj).叫();
            }
                */
            if (obj instanceof 会叫的东西) {
                ((会叫的东西) obj).叫();
                }
        }
    }
    // 在建造成类型体系后，请尝试化简这个啰嗦的方法，体会多态带来的好处
    public static void 动物都能新陈代谢() {
        for (Object obj : objects) {
            /*
            if (obj instanceof 麻雀) {
                ((麻雀) obj).新陈代谢();
            } else if (obj instanceof 喜鹊) {
                ((喜鹊) obj).新陈代谢();
            } else if (obj instanceof 蝴蝶) {
                ((蝴蝶) obj).新陈代谢();
            } else if (obj instanceof 猫) {
                ((猫) obj).新陈代谢();
            } else if (obj instanceof 狗) {
                ((狗) obj).新陈代谢();
            }
            /*
             if (obj instanceof 动物) {
                ((动物) obj).新陈代谢();
        }
    }

    /*static class 麻雀 {
        public void 新陈代谢() {
            System.out.println("新陈代谢");
        }

        public void 飞() {
            System.out.println("鸟儿飞");
        }

        public void 叫() {
            System.out.println("叽叽喳喳");
        }
    }
    */
    static class 鸟 extends 动物 implements 会飞的东西 , 会叫的东西 {
        public void 飞() {
            System.out.println("鸟儿飞");
        }
        static class 动物{
        public void 新陈代谢() {
            System.out.println("新陈代谢");
        }
    }
    static class 麻雀 extends 鸟 {
    
        }
         interface 动物 {
            void 新陈代谢();
        }

        interface 会飞的东西 {
            void 飞();
        }

        interface 会叫的东西 {
            void 叫();
            }
        }
---


##### Mixin


    public class Main {
    static class Animal {
        String Name;

        public Animal(String name) {
            this.Name = name;
        }
        public String getName() {
            return Name;
        }
    }

    interface 会跑 {
        String getName();
        default void 跑(){
            System.out.println(getName() + "跑啊跑啊跑");
        }
    }
    interface 会游泳 {
        String getName();
        default void 游泳() {
            System.out.println(getName() + "游啊游啊游");
        }
    }

    static class 猫 extends Animal implements 会跑 {
        猫() {
            super("小花猫");
        }
    }

    static class 鱼 extends Animal implements 会游泳 {
        鱼() {
            super("鱼");
        }
    }

    // 现在 乌龟 想要同时复用会跑和会游泳的代码
    // 请尝试通过接口的默认方法实现mixin
    // 从而实现最大程度的代码复用
    //
     static class 乌龟 extends Animal implements 会跑,会游泳 {
        乌龟() {
            super("乌龟");
        }
    }

    public static void main(String[] args) {
        new 乌龟().跑();
        new 乌龟().游泳();
        }
    }


## 接口实战
##### Comparable接口
comarable 自然顺序（从小到大）  
a < b ->负数
a > b ->正数
a = b ->0
对制定的列表按照升序排序


    public class Point implements Comparable<Point> {

    private final int x;
    private final int y;
    // 代表笛卡尔坐标系中的一个点
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }

        Point point = (Point) o;

        if (x != point.x) {
            return false;
        }
        return y == point.y;
    }

    @Override
    public int hashCode() {
        int result = x;
        result = 31 * result + y;
        return result;
    }

    @Override
    public String toString() {
        return String.format("(%d,%d)", x, y);
    }

    // 按照先x再y，从小到大的顺序排序
    // 例如排序后的结果应该是 (-1, 1) (1, -1) (2, -1) (2, 0) (2, 1)
    public static List<Point> sort(List<Point> points) {
        Collections.sort(points);//要实现，就需要用到Comparable这个接口进行排序
        return points;//传进一个有序的列表，排序，返回。

    }

    public static void main(String[] args) throws IOException {
        List<Point> points =
                Arrays.asList(
                        new Point(2, 0),
                        new Point(-1, 1),
                        new Point(1, -1),
                        new Point(2, 1),
                        new Point(2, -1));
        System.out.println(Point.sort(points));
    }
    //按照X排序，从小到大
    @Override
    public int compareTo(Point that) {
        //比较this 和 that
        //return this.x - that.x; 可能会发生溢出，所以错误。
        if (this.x<that.x){
            return -1;//只要是负数就可以
        }else if (this.x>that.x){
            return 1;
        }
        //return 0;说明this.x= that.x。需要按照y
        if (this.y<that.y){
            return -1;//只要是负数就可以
        }else if (this.y>that.y){
            return 1;
        }return 0;
    }
    }

##### 自定义过滤器 (提取代码的共性，通过继承来简化代码【多态】)
- 先建一个结果集
#### TreeSet
- 两个本来不相等的元素，返回零后，treeset 就会 丢失
- 不能容纳重复元素的集合 

     
     public class User {
    /** 用户ID，数据库主键，全局唯一 */
    private final Integer id;

    /** 用户名 */
    private final String name;

    public User(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public static List<User> filter(List<User> users,Predicate<User> predicate){
        List<User> results = new ArrayList<>();
        for (User user : users){
            if (predicate.test(user)){
                results.add(user);
            }
        }
        return results;
    }
    //抽象的过滤器函数

     //    public static List<User> filter(List<User>     users,Judge condition){
    //        List<User> results = new ArrayList<>();
    //        for (User user : users){
    //            if (condition.MeetTheConditions(user)){
    //                results.add(user);
    //            }
    //        }
    //        return results;
    //    }


    //    private interface Judge{//一个类里面没有方法体，就是一个接口
    //        boolean MeetTheConditions(User user);
    //
    //    }
    //    private static class IdIsEvenNumber implements Judge{
     //
     //        @Override
    //        public boolean MeetTheConditions(User user) {
    //            return user.id % 2 == 0;
    //        }
    //    }

    // 过滤ID为偶数的用户
    public static List<User> filterUsersWithEvenId(List<User> users) {
        return filter(users, (Predicate<User>) user -> user.id % 2 == 0);//匿名内部类
    //        List<User> results = new ArrayList<>();
    //        for (User user : users) {
    //            if (user.id % 2 == 0) {
    //                results.add(user);
    //            }
    //        }
    //        return results;
        }

    // 过滤姓张的用户
    public static List<User> filterZhangUsers(List<User> users) {
        return filter(users, new Predicate<User>() {
            @Override
            public boolean test(User user) {
                return user.name.startsWith("张");
            }
        });
    //        List<User> results = new ArrayList<>();
    //        for (User user : users) {
    //            if (user.name.startsWith("张")) {
    //                results.add(user);
    //            }
    //        }
    //        return results;
        }

    // 过滤姓王的用户
    public static List<User> filterWangUsers(List<User> users) {
        return filter(users, new Predicate<User>() {
            @Override
            public boolean test(User user) {
                return user.name.startsWith("王");
            }
        });
        }
    //        List<User> results = new ArrayList<>();
    //        for (User user : users) {
    //            if (user.name.startsWith("王")) {
    //                results.add(user);
    //            }
    //        }
    //        return results;
   
   
  
    // 请尝试通过Predicate接口将上述代码抽取成一个公用的过滤器函数
    // 并简化上面三个函数
    //判断是否满足一个条件使用Predicate
    //    public static List<User> filter(List<User> users, Predicate<User> predicate) {
    //
    //    }
    


##### name相同，id 不同
    public class User implements Comparable<User> {
    /** 用户ID，数据库主键，全局唯一 */
    private final Integer id;

    /** 用户名 */
    private final String name;

    public User(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }

        User person = (User) o;

        return Objects.equals(id, person.id);
    }

    @Override
    public int hashCode() {
        return id != null ? id.hashCode() : 0;
    }

    /** 老板说让我按照用户名排序 */
    @Override
    public int compareTo(User o) {
        if (this.name != o.name){
        return name.compareTo(o.name);
    }
        if (this.id > o.id){
            return 1;
        } else if(this.id<o.id){
            return -1;
        } return 0;
        }

    public static void main(String[] args) {
        List<User> users =
                Arrays.asList(
                        new User(100, "b"),
                        new User(10, "z"),
                        new User(1, "a"),
                        new User(2000, "a"));
        TreeSet<User> treeSet = new TreeSet<>(users);
        // 为什么这里的输出是3？试着修复其中的bug
        System.out.println(treeSet.size());
        }
    }
## 内部类
- 用途：实现更加精细的封装
- 可以访问外围类（包裹内部类的类）的实例方法
- 非静态内部类
- - 和一个外围类实例向绑定
- - 可以访问外围类实例的方法
- 静态内部类
- - 不和外围类实例相绑定
- - 不可以访问外围实例的方法  
**原则：永远使用静态内部类，除非编译报错**  
*原因：非静态的时候，编译器会注入一个外围类的实例（没用的时候，会占据内存）*
---
- 非静态内部类  
每一个内部类都和一个外围类（A）的变量实例相绑定，使得内部类可以毫无障碍地使用外围类的实例方法  
    private class A{  
    {log();}//内部类  
    } 
- - 因为编译器会自动添加一个外围类的实例方法，而静态内部类是需要手工添加才可以使用外围类的实例方法
 
 - 静态内部类 
 没有相绑定的外围实例，所以不能够调用外围类的实例方法  
 private static class B{  
    public B (A a){  
    this.a = a;  
    }  
    A a;  
    //创建外围类的实例方法    
    {a.log()}
 }
 
- Java类的文件只能包含一个和他同名的public成员



   在内部类中建立一个外围类的实例，建立构造器，使得内部类可以使用

 



##### 非静态内部类访问外围类
编译器注入外围类
    public class Home {
    List<Cat> cats = new ArrayList<>();

    public List<String> getCatNames() {
        CatNameCollector collector = new CatNameCollector();
        cats.forEach(collector);
        return collector.getCatNames();
    }

    // 记录日志
    private void log(Cat cat) {
        System.out.println("Collecting cat " + cat.getName());
    }

    // 在这个类里会产生一个编译错误
    // 请思考一下为什么
    // 并将此类改写成非静态的内部类，以修复此问题
    class CatNameCollector implements Consumer<Cat> {
        private List<String> catNames = new ArrayList<>();

        @Override
        public void accept(Cat cat) {
            log(cat);//log是一个实例，，编译器要访问外围实例的时候，需要和外围类实例想绑定，所以不能够使用static
            catNames.add(cat.getName());
        }

        private List<String> getCatNames() {
            return catNames;
        }
    }
    }
    
##### 静态内部类访问外围类
手工制作访问外围类的实例


    public class Home2 {
    List<Cat> cats = new ArrayList<>();

    public List<String> getCatNames() {
        CatNameCollector collector = new CatNameCollector(this);
        cats.forEach(collector);
        return collector.getCatNames();
    }

    // 记录日志
    private void log(Cat cat) {
        System.out.println("Collecting cat " + cat.getName());
    }

    static class CatNameCollector implements Consumer<Cat> {
        private Home2 home;

        public CatNameCollector(Home2 home) {
            this.home = home;
        }

        // 在这个类里会产生一个编译错误
        // 请思考一下为什么
        // 不要将此类改写成非静态的内部类
        // 而是引入一个外围类的实例以调用外围类的实例方法
        // private Home2 home;
        private List<String> catNames = new ArrayList<>();

        @Override
        public void accept(Cat cat) {
            home.log(cat);
            catNames.add(cat.getName());
        }

        private List<String> getCatNames() {
            return catNames;
        }
    }
}

## 匿名内部类
- 直接通过new的方法创建的无名类
- 可以方便通过简短的代码让逻辑和外围逻辑结合起来
- 可以访问外围类的实例
    


    public class User {
    /** 用户ID，数据库主键，全局唯一 */
    private final Integer id;

    /** 用户名 */
    private final String name;

    public User(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    public Integer getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    // 这里使用了一个NameCollector类，请尝试将它改写成匿名内部类
    // 使得代码更加集中，更加容易阅读
    public static List<String> collectNames(List<User> users) {
        final List<String> names = new ArrayList<>();
        NameCollector collector = new NameCollector() {
            @Override
            public void accept(User user) {
                names.add(user.getName());
            }

            public List<String> getNames() {
                return names;
            }
        };
        users.forEach(collector);
        return collector.getNames();
    }

    public static void main(String[] args) {
        List<User> users = Arrays.asList(new User(1, "a"), new User(2, "b"));
        System.out.println(collectNames(users));
    }
}