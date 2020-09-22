---
title: "Queue"
date: 2020-09-18T16:19:46+08:00
draft: true
tags: ["",""]
---
## poll
- `remove head of this queue`
- 不同于 `remove` 的是当queue为空 的时候会返回null
## remove
- 移出队列的顶元素，队列为空则会抛出异常

## offer()
- 在尾部添加元素
## Queue 常用方法
||||
|--|--|--|--|
add   |      增加一个元索        |              如果队列已满，则抛出一个IIIegaISlabEepeplian异常|
remove |  移除并返回队列头部的元素|     如果队列为空，则抛出一个NoSuchElementException异常|
element  |返回队列头部的元素      |        如果队列为空，则抛出一个|NoSuchElementException异常
offer     |  添加一个元素并返回true     |   如果队列已满，则返回false|
poll  |       移除并返问队列头部的元素  |   如果队列为空，则返回null|
peek    |   返回队列头部的元素       |       如果队列为空，则返回null|
put    |     添加一个元素         |              如果队列满，则阻塞|
take  |      移除并返回队列头部的元素    |