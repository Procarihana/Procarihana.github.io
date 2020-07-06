---
title: "Java 面向对象初步"
date: 2020-05-11T17:18:01+08:00
draft: false
---
## 面向对象的三大特征
### 封装
优点：实现细节可以发生变化，只要接口不变，就不会对使用产生影响
### 继承
### 多态
## 封装
### 封装隐藏了内部实现细节，只暴露出接口
#### 电灯对象
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



   
    return gender.equals("M");
    return Object.equals.(gender,"M");//保证gender是空指针，也不会抛出空指针异常
    return "M".equals(grnder);

### 不封装
- 暴露细节的结果就是无法变化
- 软件的演进例子：为原先的属性增加限制
- 软件的演进例子： 修改某些属性


## 封装的实现
### 访问控制符

###### 包：如同一个文件夹，每个类在包里面，只能在包中被看到，包外是无法被看到的。有一个接口和外界交互，外界不能访问包里面的内容
-  public 任何人都能访问
-  protected 只有子类可以访问和同一个包的可以访问（子类访问通过extend）
-  package private 只有同一个包的类可以访问
-  private 只有自己可以访问
### JavaBean约定
-  getter
-  setter

## 设计模式实战：工厂方法
- 使用静态工厂方法代替构造器
- 将构造器私有化
## 类的访问限定符
- public 任何访问
- package private 同一个包的类可以访问
- - 包之间是没有父子关系的
- （包之间没有任何关系）
- private inner class 只能在同一个类中访问
- 了解Java Platform Module System

### 用中间类绕过包级私有的限制

## 封装的必要性
- 软件的演进 —— 向外界暴露内部实现类的风险
## 扩展阅读
- JSON序列化/反序列化与JavaBean约定
- setter的链式调用
- builder 模式
