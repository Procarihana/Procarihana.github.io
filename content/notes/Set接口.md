---
title: "Set接口"
date: 2020-09-17T10:13:05+08:00
draft: true
tags: ["",""]
---
## addAll
- 元素不重复
- addAll（Colletcion）
- 如果set里面原本有不属于这个集合的元素，就会被移出，剩下的是原本的set和新集合的交集
- 不允许添加包含null的集合，否则会出现exception
## Add
- add（Object）
- 通过判断Objcet 是否为 null 且是否和集合里面的元素相等，如果Object 已经存在于set里面就不会发生改变并且return false

## remove
- 如果存在这个元素 移出后return true