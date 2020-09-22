---
title: "MYSQL记录合集"
date: 2020-09-06T15:43:53+08:00
draft: true
toc: false
images:
tags: [MYSQL]
---

1.联接查询作为数据

insert into AddressList (name,address)
select a.UserName,b.address from SYSTEM_Users a
inner join BASE_Customer b on a.code=b.code


问题：
- "Error attempting to get column 'goods_name' from result set.  Cause: java.sql.SQLDataException: Cannot determine value type from string '桃子'\n; Cannot determine value type from string '桃子'; nested exception is java.sql.SQLDataException: Cannot determine value type from string '桃子'"
- 缺少无参数构造函数
- moudle 四件套
- - @Builder
- - @Data
- - @AllArgsConstructor
- - @NoArgsConstructor

# 记录

## Select
1. select ~ from ~ right join ~ on ~ group by ~ order by ~ 
- as 别名
- case when 条件 then 满足条件结果 else 不满足条件结果
- case when ? is ? then else
- A right join B == B + A 和 B 的交集 == goods_id,goods_name
- - A -> order 
>- order ： id,goos_name,pricr, qualitity,user_id
- - B -> goods : id, gname
- on A 和 B 重合的部分，作为两表合并后数据正确合并的依据
- group by 新出来的临时表能够有一个 primary key
- order by 正序排列 L-> Big
- - order by ~ desc 倒序排列 Big -> L
```java
select goods.name as goods_name,
        (case when sum(o.price * o .quantity) is null then 0 else sum(o.price * o .quantity) end) as total_price
        from `order` o right
        join goods
        on goods.id = o.goods_id
        group by goods.id order by total_price desc
```