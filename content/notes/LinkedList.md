---
title: "LinkedList"
date: 2020-09-18T17:39:03+08:00
draft: false
tags: ["List","LinkedList"]
---

## 属性
#### 初始值为0

## void push（）
- 在链条的头部添加元素
- addFirst（）

## boolrean offer()
- 在尾部添加元素
- 实现Queue接口的offer
## push()
- 实质上就是addFirst()

## offerLast()/offerFirst ()
-  实质上执行的addLast()和addFirst()`return true`


## peekFirst()/peekLast()
- 查看第一个或者最后一个元素，如果list为空则返回null，除此以外什么都不做

## poll（）
- 检索和删除顶元素，如果为null 返回null

## pop（）
- 删除列表的顶元素，如果为null ，则抛出异常

## void add(int index, E element)
- LinkedList 并没有add（），但是在继承的AbstractSequentiaList里面 有实现add() 
- - add(0, element) 就是把 element 放在first， 原本的first 就会变成second
- - add(2 ,element) 就是把element 放在第二个的右边，成为第三个元素，原本的第三个就会变成第四个 