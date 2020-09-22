---
title: "List"
date: 2020-09-11T20:17:55+08:00
draft: false
tags: ["ArrayList","LinkedList","RadomAccess"]
---
## List
#### add
- 添加元素在右边（最后）
## LinkedList 和 ArrayList 区别
#### ArrayList
- 继承 AbstraciList （实现List接口中除了size()和get（int location）之外的方法和Collection接口）
```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable{
```
#### LinkdeList
- 继承 AbstractSequentialList（继承 AbstractCollectionm,实现了“链表中，根据index索引值操作链表的全部方法”）Deque接口）
- 双端 --> 可以被当做站、队列或者双端队列使用
```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    transient int size = 0;
    /**
     * Pointer to first node.
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
    transient Node<E> last;

    /**
     * Constructs an empty list.
     */
```
### RadomAccess
- List 子类实现了 RandomAccess 接口，那表示它能快速随机访问存储的元素， 这时候你想到的可能是数组，通过下标 index 访问，实现了该接口的 ArrayList 底层实现就是数组，同样是通过下标访问，只是我们需要用 get() 方法的形式， ArrayList 底层仍然是数组的访问形式。
- - ArrayLise数组支持随机访问， 查询速度快， 增删元素慢
- - LinkserList链表支持顺序访问， 查询速度慢， 增删元素快。
- - 随机访问列表使用循环遍历，顺序访问列表使用迭代器遍历
**在源码里注解的确说明了这点，实现了RandomAccess的索引遍历快于迭代遍历，因为LinkedList要移动指针**