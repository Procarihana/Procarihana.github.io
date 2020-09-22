---
title: "ArryList"
date: 2020-09-16T23:52:09+08:00
draft: true
tags: ["ArryList",""]
---

#### 初始容量为 10
- 之后每超过10就添加16个
#### 有设置容量
- 意义在于先准备一个空间，避免以后不断地扩容造成空间浪费
- 如果指定初始容量a，数组的容量就为a
- 如果初始容量为0或者没有指定，就为0
- 负数就抛出异常
```java
 public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
```
#### 扩容的时候自动调整大小
- ArrayList 里面有一个Object[] elementData 用于存储实际的元素
- - 包含size
- 扩容过程
>- 执行add（）的时候会先判断当前未添加前的elementDate.length 是否会等于初始容量 size，如果等于就执行grow()扩容，并把扩容后的新对象组size 赋值到 elementData 里面
>-  Object[]grow（） 增长的最小的目标容量（minCapacity）是当前的目标容量+1
>- - 之后执行Object[] grow(int minCapacity) 。先复制就的Array，然后加入扩充好的新容量的数组（newCapacity（int minCapacity））里，复制给elementData并返回这个新的数组
>- - - newCapacity(int minCapacity) 新容量的目标容量为旧容量的1.5倍，然后把最小目标容量和新容量做对比，如果新容量小的话

#### Array 随机访问 查询都是O(1)
`Constructs an empty list with an initial capacity of ten.`
#### 一个包含指定元素的List，通过迭代的方式返回元素的顺序
#### 如果为空则返回NollPointException

####  boolean Add
- 添加在 list 的尾部
- 如果能够成功加入 return true
#### set(int index, E element)
- 更新。
- - 更新指定下标位置的值

#### remove

- size-1
