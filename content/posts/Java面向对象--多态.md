---
title: "Java面向对象————多态"
date: 2020-05-11T17:20:16+08:00
draft: false
tags: ["多态","面向对象"]
---

## 面向对象的三大特征
##### 封装
##### 继承
##### 多态
多态：不同类的不同的实现方法
      
      方法名字变量都一样，但是子类继承后的实现不一样


重载：两个类同一个方法的不同实现,根据最接近的类型进行匹配
      
      方法名相同，参数表不一样
      


## 多态
##### 实例方法本身是多态的
实例：一个方法不是静态的就是实例方法  
通过区分有无Statci

- 在运行时根据this（当前对象的实际类型）来决定调用哪个方法
[]方法接收者

obj.method(1)

消息接受者.消息（参数）//接受者会根据消息的不同自行决定如何响应消息
- 静态方法没有多态
- 参数静态绑定，接受者动态绑定
覆盖：重写父类中的方法，方法名、参数不能改变，但是返回值可以改变
- - 多态只对方法的接受者生效
- - 多态只选择接受者的类型，不选择参数的类型

```java
    public class Base {
    public void print(ParamBase param) {
        System.out.println("I am Base,the param is ParamBase");
    }
    public void  print(ParamSub param){
        System.out.println("I am Base,the param is ParamSub");
         }
    }


    public class Sub extends Base {
    @Override
    public void print(ParamBase param){
        System.out.println("I am Sub,the param is ParamBase");
    }
    @Override
    public void print(ParamSub param){
    //方法名一样，参数不一样（重载），但是两个又互为父子类，有继承关系
        System.out.println("I am Sub,the param is ParamSub");
       }
    }
   
```java 
    public static void main(String[] args) {
       //声明一个父类的对象，但是建立一个子类的对像，因为任何的子类都是父类的成员
        Base object = new Sub();
        
        //声明一个父类的参数，但是创建一个子类的产数给他
        ParamBase param = new ParamSub();
      
        object.print(param);
        /*消息的接受者是多态类型的，有继承类，参数也同样有父子关系
        消息的接受者通过动态绑定为子类object，
        所以调用的是子类的方法。
        而子类的方法是多态，又因为方法是在编译时期由参数的类型静态绑定的
        在ParamBase param = new ParamSub();虽然用的是子类的变量，但是设定的是父类的变量类型，所以父类的最接近的类，根据重载选择最适合的原则选择执行父类，
        所以调用的是子类中的public void print(ParamSub param)
        */
    }

- 例子
- - Shape的多态
- - HashSet.addAll()  


## 设计模式实战：策略模式
让代码更加简洁


    public class PriceCalculator {
    // 使用策略模式重构这个方法，实现三个策略：
    // NoDiscountStrategy 不打折
    // Discount95Strategy 全场95折
    // OnlyVipDiscountStrategy 只有VIP打95折，其他人保持原价
    // 重构后的方法签名：
    public static int calculatePrice(DiscountStrategy strategy, int price, User user) {
        return strategy.discount(price,user);
    }
    }
--- 
         public static int calculatePrice(String discountStrategy, int price, User user) {
            switch (discountStrategy) {
                case "NoDiscount":
                    return price;
                case "Discount95":
                    return (int) (price * 0.95);
                case "OnlyVip":
                    {
                        if (user.isVip()) {
                            return (int) (price * 0.95);
                        } else {
                            return price;
                        }
                   }
               default:
                   throw new IllegalStateException("Should not be here!");
                   

- 策略和业务分离



    public class User {
    private String name;
    private boolean vip;

    private User(String name, boolean vip) {
        this.name = name;
        this.vip = vip;
    }

    public static User vip(String name) {
        return new User(name, true);
    }

    public static User dios(String name) {
        return new User(name, false);
    }

    public String getName() {
        return name;
    }

    public boolean isVip() {
        return vip;
    }
    }
```


- 线程池策略：（解耦合）
1. 放弃
2. 让线程者执行
3. 把最久的策略丢弃
4. 把新的策略丢弃
##### 策略模式例子：打折策略
- 涉及金钱的时候不能够用Double；
- Price * 100 当成分，用int处理
- BigDecim 大的十进制（十进制精确的表述，小数的计算）
##### JDK线程池中的策略THreadPoolExecutor

## 多态实战
- Files.walkFileTree

不想把接口的所有方法都实现，都要查找他的默认实现

```java     
    public class FileFilter {
    public static void main(String[] args) throws IOException {
        Path projectDir = Paths.get(System.getProperty("user.dir"));
        Path testRootDir = projectDir.resolve("test-root");
        if (!testRootDir.toFile().isDirectory()) {
            throw new IllegalStateException(testRootDir.toAbsolutePath().toString() + "不存在！");
        }

        List<String> filteredFileNames = filter(testRootDir, ".csv");
        System.out.println(filteredFileNames);
    }

    /**
     * 实现一个按照扩展名过滤文件的功能
     *
     * @param rootDirectory 要过滤的文件夹
     * @param extension     要过滤的文件扩展名，例如 .txt
     * @return 所有该文件夹（及其后代子文件夹中）匹配指定扩展名的文件的名字
     */
    public static List<String> filter(Path rootDirectory, String extension) throws IOException {
        List<String> names = new ArrayList<>();
        Files.walkFileTree(rootDirectory, new SimpleFileVisitor<Path>() {
            @Override
            public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException {
                if (file.getFileName().toString().endsWith(extension)) {
                    names.add(file.getFileName().toString());
                }
                return FileVisitResult.CONTINUE;
            }
        });
        return names;
               }
            }
    //匿名内部类，把相近的两块逻辑组合到一起，直接把访问方法，使用结果。
   
            static class FileFilterVisitor extends SimpleFileVisitor<Path> {
                private String extension;
                private List<String> filteredNames = new ArrayList<>();
    
                public FileFilterVisitor(String extension) {
                this.extension = extension;
                }
    
                public List<String> getFilteredNames() {
                return filteredNames;
             }
 ```            
