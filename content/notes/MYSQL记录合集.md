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

---
- 行转列

1. 建表
```
create database test_school;
use test_shcool;
create table source(
        id varchar(20) primary key auto_increment,
        teacher_id varchar(20),
        week_day varchar(20),
        has_course varchar(20)      
)auto_increment=1;
insert into source(teacher_id,week_day,has_course)value
(1,2,"Yes"),
(1,3,"Yes"),
(2,1,"Yes"),
(3,2,"Yes"),
(1,2,"Yes");
```
2. 行转列
```
select teacher_id 
case when week_day=1 then has_course end mon,
case when week_day=2 then has_course end tue,
case when week_day=3 then has_course end thi,
case when week_day=4 then has_course end thu,
case when week_day=5 then has_course end fri
from source;
```
- 添加索引
1. 建表

```
create table a_test(
        fdate datatime,
        value int
);

insert into a_test(
        fdate,value
) value("2018/11/23",10),
("2018/12/31",3),
("2019/2/9",53);
```
2. 索引

```
create idnex id_fdate on a_test(fdate);
```
3. 

```
select fyear,fmonth,value,
sum(value) over (partition by fyear order by fmonth) ysum,
sum(value) over (order by date) sum form (select yfate, year(FDATE) ad FYEAR, month(Fdate) asFMONTH, sum(value) as value rfrom A2 group by Fyear,Fmonth Order by Fyear , Fmonth) b;
```

#### 大于号小于号
- < 和 > 分别对应 &lt;  和  &gt;
- 积分result表中有A B C D四列，要求：
1）当A列值大于等于B列时，选择A列否则选择B列
2）当C列值大于等于D列时，选择C列否则选择D列
- select (case when A&gt;=B then A else B end) MAX_AB, (case when C&gt;=D then C else D end) MAX_CD from result

#### 数据库字符串的索引开始于 1 CHARINDEX
- charindex（用于判断一个字符串中是否包含另一个字符串）   
 
 
 基本语法如下：

- CHARINDEX ( expressionToFind , expressionToSearch [ , start_location ] )
expressionToFind ：目标字符串，就是想要找到的字符串，最大长度为8000 。  

 本题中CHARINDEX函数可以查找指定字符的位置，查找不到返回0，然而在mysql这样的数据库管理系统中，用SUBSTRING_INDEX方法代替，另外Len在mysql中对应LENGTH。不同的DBMS测试方式不同。
- - 查询显示雇员的姓名和姓名中是否含有字母A的信息，满足如下条件
如果字符A在姓名的首位，则显示'字符A在首位'
如果字符A在姓名的末位，则显示'字符A在末位'
如果字符A在姓名中不存在，则显示'没有字符A'
其他情况显示'字符A在中间'
```
select ename, case charindex('A',ename) when 1 then '字符在首位' when len(ename) then '字符在末位' when 0 then '没有字符A' else '字符A
```



#### datediff()
- DATEDIFF() 函数返回两个日期之间的时间。startdate 和 enddate 参数是合法的日期表达式。
- datapart 有相应对应的值 年月日 季度 年中的日等等
- 语法
`DATEDIFF(datepart,startdate,enddate)`
- 取出 BORROW表中日期(RDATE字段)为当天的所有记录？(RDATE字段为datetime型，包含日期与时间)。
- RDDATE包括日期和时间，这里因为有时间，和getdate不能直接比较，所以用datediff转换为范围。
```
select * from BORROW where datediff(dd,RDATE,getdate())=0
```
#### SQL 执行顺序
- 写法顺序：select--from--where--group by--having--order by 
- 执行顺序：from--where--group by--having--select--order by

#### All and  Any
- All：对所有数据都满足条件，整个条件才成立；
- Any：只要有一条数据满足条件，整个条件成立；
- Some的作用和Any一样 .
- - 运动会比赛信息的数据库，有如下三个表：
运动员ATHLETE（运动员编号 Ano，姓名Aname，性别Asex，所属系名 Adep）， 
项目 ITEM （项目编号Ino，名称Iname，比赛地点Ilocation）， 
成绩SCORE （运动员编号Ano，项目编号Ino，积分Score）。
写出目前总积分最高的系名及其积分，
```
select adep, sum(SCORE) from athlete ,SCORE where ATHLETE.Ano=SCORE.Ano GROUP BY Adep having sum(SCORE)>=ALL(select sum(SCORE) from ATHLETE where ARHLETE.Ano=SCORE.Ano grounp by Adep)
```

#### Group by
- group by id --> id相同的就会只剩下一个，达到行转列的目的
- - 有一张学生成绩表sc（sno 学号，class 课程，score 成绩），请查询出每个学生的英语、数学的成绩（行转列，一个学生只有一行记录）。
```
select sno, sum(if(class='english',score,0))as english,
sum(if(class='math',score,0))as math,from sc where class in('english,math')group by sno
```
```
select sno, case when class='english' then score else 0 end, case when class='math' then score elst 0 end from sc where class in ('english','math') group by sno
```
https://www.nowcoder.com/questionTerminal/9e3a3d92116e4f47858a9691ca75af96

#### where and having
- where 不能用在连接聚合函数
- having 要放在 group by 后面
- - 有订单表orders，包含字段用户信息userid，字段产品信息productid，以下语句能够返回至少被订购过两次的productid？
```
select productid from orders having count(productid) > 1
```

#### truncat
- 处理效率：drop>trustcate>delete
- drop删除整个表；trustcate删除全部记录，但不删除表；delete删除部分记录
- delete不影响所用extent，高水线保持原位置不动；trustcate会将高水线复位。
>- 在使用truncate时一定要注意，虽然可以恢复，但为了减少麻烦，还是要慎重。
> - 如果想删除部分数据用delete，注意带上where子句，回滚段要足够大；
> - 如果想保留表而将所有数据删除，如果和事务无关，用truncate即可；
>- 如果和事务有关，或者想触发trigger，还是用delete；
>- 如果是整理表内部的碎片，可以用truncate跟上reuse stroage，再重新导入/插入数据。

#### in and =
- 多个返回查询不能用 in 要用 =
- - 表A(仅列出部分数据作参考)
Order_id     User_id    Add_time
11701245001 10000    1498882474
11701245002 10001    1498882475
表B：(仅列出部分数据作参考)
id     Order_id     goods_id price
1   11701245001    1001     10
2   11701245001    1002     20
3   11701245002    1001     10
问：用SQL查询 购买过goods_id 为1001的用户user_id（）
```
select User_id from A where Order_id in (select Order_id from B where goods_id='1001') 
```

#### 很是复杂的慢慢参透
- https://www.nowcoder.com/test/question/done?tid=38665465&qid=323540#summary

1. from app group by app_id, version_code;
2. select app_id, version_code, sum(download_count) as sum_download_count from app group by app_id, version_code;
3. selecr app_id, version_code, sum(download_code) sum_download_cade from app group by app_id, version_code order by sum_download_code;
4. (select app_id,version_code,sum(download_code) sum_download_code from app group by app_id,version_code order by sum_download_code desc)as t group by t.app_id, 

