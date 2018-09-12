我们要查询广东省的所有的市下的所有区
```
select e.id,p.cityName,s.cityName,e.cityName from s_provinces e
join s_provinces s on e.parentId = s.id
join s_provinces p on s.parentId = p.id
where p.cityName = '广东省';
```
我们要查出广东省的所有市并且用union与查询所有的区连起来
```
select p.id,s.cityName,p.cityName cityName from
s_provinces p
join s_provinces s on p.parentId = s.id
where s.cityName = '广东省'
```
因为要使用union将表连起来，两条查询表的列数要相等，所以我们在查询所有的市的时候要加'null'一列
```
select p.id,s.cityName,p.cityName,null cityName from s_provinces p
join s_provinces s on p.parentId = s.id
where s.cityName = '广东省'
```
之后就可以用union连接
```
select p.id,s.cityName,p.cityName,null cityName from s_provinces p
join s_provinces s on p.parentId = s.id
where s.cityName = '广东省'
union
select e.id,p.cityName,s.cityName,e.cityName from s_provinces e
join s_provinces s on e.parentId = s.id
join s_provinces p on s.parentId = p.id
where p.cityName = '广东省';
```
union与union all的区别：
Union：对两个结果集进行并集操作，不包括重复行，同时进行默认规则的排序；
Union All：对两个结果集进行并集操作，包括重复行，不进行排序；
