首先，我们先把地区表把省、市、县和省、市分离出来，最后把他们连一起创一个表 。先用create table lagou_city as 第一条查询语句，先创建一个表出来，在使用insert into lagou_city添加第二条查询语句。代码如下：
```
select e.id,p.cityName as province,s.cityName as city,e.cityName as district from s_provinces e
join s_provinces s on e.parentId = s.id
join s_provinces p on s.parentId = p.id
where p.depth = 1
```
```
select p.id,s.cityName as province,p.cityName as city,null as district from s_provinces p
join s_provinces s on p.parentId = s.id
where s.depth = 1;
```
```
create table lagou_city as
select e.id,p.cityName as province,s.cityName as city,e.cityName as district from s_provinces e
join s_provinces s on e.parentId = s.id
join s_provinces p on s.parentId = p.id
where p.depth = 1

insert into lagou_city
select p.id,s.cityName as province,p.cityName as city,null as district from s_provinces p
join s_provinces s on p.parentId = s.id
where s.depth = 1;
```
分完之后我们来分离已经出来好的lagou表：
一、我们先分离创建公司表，把有关公司的字段查询出来然后创表。
```
create table company as
select company_id,company_short_name,company_full_name,company_size,financestage from lagou_result;
```
二、剩下的lagou表还有职位表和地区表，现在我们要把刚刚创建的地区表和lagou表的地区进行整合：
一共有两种情况：
1、县级为空
```
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
from (select * from lagou_city where district is null) p
  join lagou_city c on c.city like concat(p.city, '%') and c.district is null;
```
2、县级不为空
```
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
from (select * from lagou_city where district is not null) p
  join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%');
```
最后把他们建成一个表
一共有两种方法：
一种是用unoin all来连接表，另一种方法是先把一条语句创建表然后再用insert into 表名 添加数据
```
create table lagou_positions
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
from (select * from lagou_city where district is null) p
  join lagou_city c on c.city like concat(p.city, '%') and c.district is null;

insert into lagou_position
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
from (select * from lagou_city where district is not null) p
  join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%');
```
我建议使用第二种方法。