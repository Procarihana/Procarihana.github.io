---
title: "Enum"
date: 2020-09-14T21:56:22+08:00
draft: true
tags: ["",""]
---


## 题目
1. 
```java
enum AccountType
{
    SAVING, FIXED, CURRENT;
    private AccountType()
    {
        System.out.println(“It is a account type”);
    }
}
class EnumOne
{
    public static void main(String[]args)
    {
        System.out.println(AccountType.FIXED);
    }
}
```
- 所有的枚举值都是类静态常量，在初始化时会对所有的枚举值对象进行第一次初始化。
- 枚举类在后台实现时，实际上是转化为一个继承了java.lang.Enum类的实体类，原先的枚举类型变成对应的实体类型，上例中AccountType变成了个class AccountType，并且会生成一个新的构造函数，若原来有构造函数，则在此基础上添加两个参数，生成新的构造函数，如上例子中：
```java
private AccountType(){ System.out.println(“It is a account type”); }
```
会变成：

private AccountType(String s, int i){
    super(s,i); System.out.println(“It is a account type”); }
```
而在这个类中，会添加若干字段来代表具体的枚举类型：
```java
public static final AccountType SAVING;
public static final AccountType FIXED;
public static final AccountType CURRENT;
```
而且还会添加一段static代码段：
```
static{
    SAVING = new AccountType("SAVING", 0);
    ...  CURRENT = new AccountType("CURRENT", 0);
   $VALUES = new AccountType[]{
         SAVING, FIXED, CURRENT
    } }
```
以此来初始化枚举中的每个具体类型。（并将所有具体类型放到一个$VALUE数组中，以便用序号访问具体类型）
在初始化过程中new AccountType构造函数被调用了三次，所以Enum中定义的构造函数中的打印代码被执行了3遍。