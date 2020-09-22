---
title: "Stack"
date: 2020-09-11T20:06:05+08:00
draft: false
tags: ["stack",""]
---

## stack
- stack 继承Vector
#### Vector
- Vector 是 AbstractList 子类
```java
public class Vector<E>extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable{
```
- Vector 实现了 List 接口  
- - ArrayList 也是继承了 AbstructList<>
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{
```
---> Stack 和 ArrayList 都是继承 AbstructList ---> Stack 本质也是一个 List
- List 子类实现了 RandomAccess 接口，那表示它能快速随机访问存储的元素， 这时候你想到的可能是数组，通过下标 index 访问，实现了该接口的 ArrayList 底层实现就是数组，同样是通过下标访问，只是我们需要用 get() 方法的形式， ArrayList 底层仍然是数组的访问形式。
- stack  的 search 能够通过下标查找对象
## Stack 方法
-  初始化
```java
Stack stack = new Stack(); 
Stack<> stack = new Stack<>();
```
- 栈顶元素出栈，并返回
```java
public synchronized E pop() {
        E       obj;
        int     len = size();

        obj = peek();
        removeElementAt(len - 1);

        return obj;
    }
```
- 判断是否为空
```java
isEmpty()
```
- 添加元素
- - push 和 list add 实现是一样的
```java
push(ElementItem item)
```
- 获取栈顶值，元素不出栈（栈为空时抛出异常）就喵一眼啥也不干
```java
 public synchronized E peek() {
        int     len = size();

        if (len == 0)
            throw new EmptyStackException();
        return elementAt(len - 1);
    }
```
- 是否存在object
- - 返回值为int，若存在，返回值为obj距离栈顶的位置，若不存在，返回 -1
```java
 public synchronized int search(Object o) {
        int i = lastIndexOf(o);

        if (i >= 0) {
            return size() - i;
        }
        return -1;
    }
```
- stack 作为list 也具备List 常用方法
```java
//获取stack长度
size()
//下标处添加
add(int index, E element)
//添加集合
addAll(Collection<? extends E> c)
//移除对象
remove(Object obj)
//根据下标移除对象
remove(int index)
//清空
clear()
```