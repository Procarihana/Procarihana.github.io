---
title: "Java 面向对象初步——封装与隐藏"
date: 2020-05-11T17:18:58+08:00
draft: false
---
## 面向对象的三大特征
### 封装
优点：实现细节可以发生变化，只要接口不变，就不会对使用产生影响
理念：对外暴露尽可能少的细节
### 继承
### 多态
## 封装
### 封装隐藏了内部实现细节，只暴露出接口
#### 电灯对象
高、低耦合
- 你只访问他的“开关”接口，不关心内部的“电路”细节


       public static void main(String[] args) {
        Light light = new Light();
        light.turnOn();//低耦合

        Light light2 = new Light();
        light2.打开电路1();
        light2.打开电路2();
        light2.打开电路3();
        //高耦合,比低耦合更难扩展

        //Home类和Light类之间建立联系——>耦合（让改变变得困难）
        //一方进行改变时，另一方也需要进行改变
        
        public class Light {
    public void turnOn(){//接口
        打开电路1();
        打开电路2();
        打开电路3();//细节

    }
    public void 打开电路1(){

    }
    public void 打开电路2(){

    }
    public void 打开电路3(){

    }

废弃后使用自动停止，不影响其他代码的运行

    public class Light {
    @Deprecated
    public void turnOn(){//接口
        打开电路1();
        打开电路2();
        打开电路3();//细节
        
    }
    public static void main(String[] args) {
        Light light = new Light();
light.~~turnOn()~~;//低耦合
#### 汽车
- 你只访问他的“方向盘”接口，不关心内部的细节
- 可以通过@Deprecated告知方法废弃，但不会有任何错误
---


   
    return gender.equals("M");
    return Object.equals.(gender,"M");
    return "M".equals(grnder);//保证gender是空指针，也不会抛出空指针异常，因为M永远不会是空指针

### 不封装
- 暴露细节的结果就是无法变化
- 软件的演进例子：为原先的属性增加限制
- 软件的演进例子： 修改某些属性


## 封装的实现
- 外界只通过一个接口和里面进行交互，里面的实现细节用private 进行 构造器用private，保证外界不能够用new 直接进行对象的创建
### 访问控制符

###### 包：如同一个文件夹，每个类在包里面，只能在包中被看到，包外是无法被看到的。有一个接口和外界交互，外界不能访问包里面的内容
**包是没有嵌套包含关系的**
-  public 任何人都能访问
-  protected 只有子类和同一个包的类可以访问（子类访问通过extend继承）  
同一个包的类可以访问是因为封装的边界是把包内和接口外相分离
-  package private *包级私有* 只有同一个包的类可以访问
-  private 只有自己可以访问
-  包级私有  
不加任何的访问限定符

  **只有在同一个包中的类才能看到，包外的类看不到里面**
### JavaBean约定
-  getter
-  setter
-  首字母大写

     
     //非boolean属性 firstName
     public String getFristName
     public void setFristName
     
     //boolean属性male
     public boolean isMale()
     pubilc void setMale(boolean male)

#### JSON
- 互联网前后段交换的一种格式，是序列化和反序列化的实现
- 将对象用字符串表示出来的一种方法

- 序列化serialization   
把一个对象变成一段字符串、一个字节流（序列化的对象）
【序列化Picture】

- 反序列化deserialize  
把字符串或字节流转换成一个的对象
- FastJson API 


     public static void main(String[] args) {
        Cat cat = new Cat("miao",true);
        System.out.println(JSON.toJSONString(cat));//序列化
        String s = "{\"cute\":true,\"name\":\"miao\"}";
        cat = JSON.parseObject(s,Cat.class);//反序列化
    }
    
- Json的读写关键在javaBean的getter和setter、is，并非是内部的属性（成员变量）
- 如果属性是null的话，就会自动忽略掉
## 设计模式实战：工厂方法

注释有可能过时，通过方法名、变量名等方式让编译器能够读懂，使得代码更容易被阅读。

静态工厂：statci 返回要生产的对象 方法名   
**（可以任意取，通过阅读可以得知信息）**
- 使用静态工厂方法代替构造器
- 将构造器私有化
- 实际上就是封装
##### 优点：
1. 方法有名字，可以让人从中得知方法的信息，且可以被编译器处理。注释是不会被编译器编译的，还存在过时的情况
2. 不同于构造器，静态工厂方法不一定要创造一个新的对象，可以返回null或者之前创造的对象，使得


        Cat cat = new Cat();//构造器，创造实例对象。
    
    
        public static Cat newCuteCat(String name) {
        if (name == null){
            return new Cat（name,cute:true）;
        }//不需要创造一个对象，可以返回之前创造的构造器
3. 可以返回返回类型的子类型，提高静态方法的因果性；而构造器并不可以


    public class WhileCat extends Cat {

    public WhileCat(String name, boolean cute) {
        super(name, cute);
        }
    }
     public static Cat newCuteCat(String name) {
        if (name.contains("while")) {
            return new WhileCat(name, true);
    }
4. 返回的对象可以变化，根据输入的参数动态决定选择返回的预先定义好的对象，节省时间且避免每次都要重新创建新的对象的内存占用。
- 可以返回一些动态的类，通过动态加载一些未来的类
- 并没有用new创造任何对象，而是返回true和flase这两个预先定义好的对象。不需要每次都创造对象


     public class Cat {
    private static final Cat INVALID_CAT = new Cat("Invalid cat",false);
    //对象创建用来应对非法对象
    }
      public static Cat newCuteCat(String name) {
       
        if (name == null || name.isEmpty()){
        //传进的参数不合法的时候，就能够返回预先定义好的对象，避免重新创建新的对象应对
            return INVALID_CAT;
        }
        return new Class.forName("FutureCat").newInstance();
    }


- 静态工厂方法EG：  
 Boolean.valueof
 Integer.parseInt  

5. 静态工厂返回的对象可以不存在（Java动态加载）
       

    return new Class.forName("futureCat").newInstance();

##### 缺点：
1. 没有办法被子类化，不能被覆盖**（非构造器）**；但是构造器可以被子类调用
2. 很难让开发者找到（名字）

- 使用静态工厂方法代替构造器

表现出对外接口的状态
- 将构造器私有化

## 类的访问限定符 
- public 所有人都可以访问
- 不加public，什么都不添加的话，就是包级私有（包提供的封装边界）
- package private 同一个包的类可以访问
- - **包之间是没有父子关系的**（包之间没有任何关系）
- private static class InnerClass只能在同一个类中访问（也是封装的一种形式）
- 包级私有限定的是通过包名判断的，但是JVM有包名设置的限定，对于自定义的包含java在运行时是不被允许的
- 了解Java Platform Module System
模块化接口，使得包可以私有化，让内部包

##### 包级私有破坏
**创建一个名字一样的包在包里面创造一个类作为桥梁，然后return new出要访问的私有包的类。最后在Main调用就可以了**


       Eg：https://github.com/hcsp/bypass-package-private/pull/29

##### 用中间类绕过包级私有的限制

## Java模块系统简介
- JDK9的新功能，把包对外封装成模块module，提供更大范围的封装，包级私有的包就不会被破坏
## 封装的必要性
- 软件的演进 —— 向外界暴露内部实现类的风险
## 扩展阅读
- JSON序列化/反序列化与JavaBean约定
- setter的链式调用
- builder 模式 Vs构造器
- 解决变量（参数）太多的问题
- - 构造器 : 
Person person = new Person(firstName:"",lastname:"",address:"",phone:123)
- - Builder：  
person = PersonBulider.aPerson()  
         .withFirstName("")  
         .withLastName("")  
         .withAddress("");


    public class UserBuilder {
    // 请在这里使用builder模式建造User对象
    // 所需的接口请参阅UserBuilderTest测试类
    private String firstName;
    private String lastName;
    private String phoneNumber;
    private String address;

    //    private UserBuilder() {
    //
    //    }
    //把private 改成public 以及 直接删除掉，都可以让UserBuilderTest实现，那么是否这个方法不需要存在？

    public static UserBuilder anUser() {
        return new UserBuilder();//提供静态空转的方法
    }

    public UserBuilder firstName(String firstName) {
        this.firstName = firstName;
        return this;
    }

    public UserBuilder lastName(String lastName) {
        this.lastName = lastName;
        return this;
    }

    public UserBuilder phoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
        return this;
    }

    public UserBuilder address(String address) {
        this.address = address;
        return this;
    }

        public User build() {
            return new User(firstName, lastName, phoneNumber, address);
            }
        }

- builder 同样也可以设置默认值

## 书
Effictive Java
